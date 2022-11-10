## FINDINGS
NB: *Some functions have been truncated where neccessary to just show affected parts of the code*
Throught the report some places might be denoted with audit tags to show the actual place affected.

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L152-L164
### EscrowLib.sol.releaseCollateral():  The value of self.deposited[token].amount should be cached 
```solidity
File: /contracts/utils/EscrowLib.sol
152:    function releaseCollateral(

160:    ) external returns (uint256) {

163:        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); } //@audit: Initial access
164:        self.deposited[token].amount -= amount;//@audit: second access
```

```diff
diff --git a/contracts/utils/EscrowLib.sol b/contracts/utils/EscrowLib.sol
index e838d7f..cfb4a9c 100644
--- a/contracts/utils/EscrowLib.sol
+++ b/contracts/utils/EscrowLib.sol
@@ -160,8 +160,9 @@ library EscrowLib {
     ) external returns (uint256) {
         require(amount > 0);
         if(msg.sender != borrower) { revert CallerAccessDenied(); }
-        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }
-        self.deposited[token].amount -= amount;
+        uint _depositedAmount = self.deposited[token].amount;
+        if(_depositedAmount < amount) { revert InvalidCollateral(); }
+        self.deposited[token].amount = _depositedAmount - amount;

         LineLib.sendOutTokenOrETH(token, to, amount);
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L192-L207
### EscrowLib.sol.liquidate(): The value of self.deposited[token].amount should be cached 
```solidity
File: /contracts/utils/EscrowLib.sol
192:    function liquidate(

200:        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }

202:        self.deposited[token].amount -= amount;
```

```diff
diff --git a/contracts/utils/EscrowLib.sol b/contracts/utils/EscrowLib.sol
index e838d7f..92d9aef 100644
--- a/contracts/utils/EscrowLib.sol
+++ b/contracts/utils/EscrowLib.sol
@@ -197,9 +197,10 @@ library EscrowLib {
     ) external returns (bool) {
         require(amount > 0);
         if(msg.sender != self.line) { revert CallerAccessDenied(); }
-        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }
+        uint _depositedAmount = self.deposited[token].amount;
+        if(_depositedAmount < amount) { revert InvalidCollateral(); }

-        self.deposited[token].amount -= amount;
+        self.deposited[token].amount = _depositedAmount - amount;

         LineLib.sendOutTokenOrETH(token, to, amount);

```


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L137-L151
### SpigotedLine.sol.useAndRepay(): unusedTokens[credit.token] should be cached
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
137:    function useAndRepay(uint256 amount) external whileBorrowing returns(bool) {

143:      require(amount <= unusedTokens[credit.token]);
144:      unusedTokens[credit.token] -= amount;

```

```diff
diff --git a/contracts/modules/credit/SpigotedLine.sol b/contracts/modules/credit/SpigotedLine.sol
index 8c91716..748f190 100644
--- a/contracts/modules/credit/SpigotedLine.sol
+++ b/contracts/modules/credit/SpigotedLine.sol
@@ -140,8 +140,9 @@ contract SpigotedLine is ISpigotedLine, LineOfCredit, ReentrancyGuard {
       if (msg.sender != borrower && msg.sender != credit.lender) {
         revert CallerAccessDenied();
       }
-      require(amount <= unusedTokens[credit.token]);
-      unusedTokens[credit.token] -= amount;
+      uint256 _unusedTokens = unusedTokens[credit.token];
+      require(amount <= _unusedTokens);
+      unusedTokens[credit.token] = _unusedTokens - amount;

       credits[id] = _repay(_accrue(credit, id), id, amount);

```

### Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L42-L46
```solidity
File: /contracts/modules/interest-rate/InterestRateCredit.sol
42:    function _accrueInterest(
43:        bytes32 id,
44:        uint256 drawnBalance,
45:        uint256 facilityBalance
46:    ) internal returns (uint256) {
```
The above function is only called once on [Line 39](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L39)

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L167-L170
```solidity
File: /contracts/modules/credit/LineOfCredit.sol
167:    function _updateOutstandingDebt()
168:        internal
169:        returns (uint256 principal, uint256 interest)
170:    {


435:    function _createCredit(
436:        address lender,
437:        address token,
438:        uint256 amount
439:    )
440:        internal


516:    function _sortIntoQ(bytes32 p) internal returns (bool) {
```

### Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L84
```solidity
File: /contracts/utils/CreditLib.sol
84:    int256 price = IOracle(oracle).getLatestAnswer(c.token);

135:      int price = IOracle(oracle).getLatestAnswer(token);

251:      uint256 accruedToken = IInterestRateCredit(interest).accrueInterest(
```


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L64-L70
```solidity
File: /contracts/modules/factories/LineFactory.sol
64:        address s = factory.deploySpigot(address(this), borrower, borrower);
65:        address e = factory.deployEscrow(
66:            defaultMinCRatio,
67:            oracle,
68:            address(this),
69:            borrower
70:        );


96:        address s = factory.deploySpigot(
97:            address(this),
98:            coreParams.borrower,
99:            coreParams.borrower
100:        );
101:        address e = factory.deployEscrow(
102:            coreParams.cratio,
103:            oracle,
104:            address(this),
105:            coreParams.borrower
106:        );
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

   When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L223-L229
```solidity
File: /contracts/modules/credit/LineOfCredit.sol

//@audit: int128 drate,uint128 frate
223:    function addCredit(
224:        uint128 drate,
225:        uint128 frate,
226:        uint256 amount,
227:        address token,
228:        address lender
229:    )


//@audit: uint128 drate,uint128 frate
247:    function setRates(
248:        bytes32 id,
249:        uint128 drate,
250:        uint128 frate
251:    )
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L77-L86
```solidity
File: /contracts/utils/LineFactoryLib.sol

//@audit: uint8 revenueSplit
77:    function deploySecuredLine(
78:        address oracle, 
79:        address arbiter,
80:        address borrower, 
81:        address payable swapTarget,
82:        address s,
83:        address e,
84:        uint ttl, 
86:        uint8 revenueSplit
87:    ) external returns(address){
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L169
```solidity
File: /contracts/utils/SpigotedLineLib.sol

//@audit:  uint8 defaultSplit
169:    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L109-L113
```solidity
File: /contracts/utils/CreditLib.sol

//@audit: uint8 decimals
109:  function calculateValue(
110:    int price,
111:    uint256 amount,
112:    uint8 decimals
113:  )
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L42-L46
```solidity
File: /contracts/modules/factories/LineFactory.sol

//@audit: uint32 minCRatio
42:    function deployEscrow(
43:        uint32 minCRatio,
44:        address owner,
45:        address borrower
46:    ) external returns (address) {
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L78
```solidity
File: /contracts/modules/interest-rate/InterestRateCredit.sol

//@audit: uint128 dRate,uint128 fRate
74:    function setRate(
75:        bytes32 id,
76:        uint128 dRate,
77:        uint128 fRate
78:    ) external onlyLineContract returns (bool) {
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L101
```soliditity
File: /contracts/utils/SpigotedLineLib.sol
101:          uint256 diff = oldClaimTokens - newClaimTokens;
```

```diff
diff --git a/contracts/utils/SpigotedLineLib.sol b/contracts/utils/SpigotedLineLib.sol
index ac86b45..a46d9e8 100644
--- a/contracts/utils/SpigotedLineLib.sol
+++ b/contracts/utils/SpigotedLineLib.sol
@@ -98,7 +98,10 @@ library SpigotedLineLib {

         // used reserve revenue to repay debt
         if(oldClaimTokens > newClaimTokens) {
-          uint256 diff = oldClaimTokens - newClaimTokens;
+          uint256 diff;
+          unchecked {
+           diff = oldClaimTokens - newClaimTokens;
+          }
           // used more tokens than we had in revenue reserves.
           // prevent borrower from pulling idle lender funds to repay other lenders
```
The above operation cannot underflow due to the check on [Line 100](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L100)
that checks that the value  of `oldClaimTokens` is greater than `newClaimTokens` before perfoming the operation


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L109
```solidity
File: /contracts/utils/SpigotedLineLib.sol
109:            unused - diff
```
The above operation cannot underflow due to the check on [Line 105](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L105)
that checks that the value  of `unused` is greater than `diff` before perfoming the operation

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L164
```solidity
File: /contracts/utils/EscrowLib.sol
164:        self.deposited[token].amount -= amount;
```
The above operation cannot underflow due to the check on [Line 163](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L163)
that checks that the value  of `self.deposited[token].amount` is greater than `amount` before perfoming the operation


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L202
```solidity
File: /contracts/utils/EscrowLib.sol
202:         self.deposited[token].amount -= amount;
```

The above operation cannot underflow due to the check on [Line 200](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L200)
that checks that the value  of `self.deposited[token].amount` is greater than `amount` before perfoming the operation

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L122
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
122:            unusedTokens[credit.token] -= repaid - newTokens;
```

The operation `repaid - newTokens` cannot underflow due to the check on [Line 120](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L120) that ensures that `repaid` is greater than `newTokens` before perfoming the subtraction 

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L125
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
125:            unusedTokens[credit.token] += newTokens - repaid;
```

The operation `newTokens - repaid` would only be performed if `newTokens` is greater than `repaid`

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L144
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
144:      unusedTokens[credit.token] -= amount;
```

The operation `unusedTokens[credit.token] - amount` cannot underflow as we have a check on [Line 143](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L143) that ensures that the value of `unusedTokens[credit.token]` is greater than that of `amount`

### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L179-L196
```solidity
File: /contracts/modules/credit/LineOfCredit.sol
        for (uint256 i; i < len; ++i) {
            id = ids[i];


            // null element in array from closing a position. skip for gas savings
            if(id == bytes32(0)) { continue; }


            (Credit memory c, uint256 _p, uint256 _i) = CreditLib.getOutstandingDebt(
              credits[id],
              id,
              oracle_,
              interestRate_
            );
            // update total outstanding debt
            principal += _p;
            interest += _i;
            // save changes to storage
            credits[id] = c;
        }
```

```diff
diff --git a/contracts/modules/credit/LineOfCredit.sol b/contracts/modules/credit/LineOfCredit.sol
index 21ef853..b6c6e36 100644
--- a/contracts/modules/credit/LineOfCredit.sol
+++ b/contracts/modules/credit/LineOfCredit.sol
@@ -176,7 +176,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
         address oracle_ = address(oracle);  // gas savings
         address interestRate_ = address(interestRate); // gas savings

-        for (uint256 i; i < len; ++i) {
+        for (uint256 i; i < len;) {
             id = ids[i];

             // null element in array from closing a position. skip for gas savings
@@ -193,6 +193,9 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
             interest += _i;
             // save changes to storage
             credits[id] = c;
+            unchecked {
+                ++i;
+            }
         }
     }
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L203-L207
```solidity
File:/contracts/modules/credit/LineOfCredit.sol
203:        for (uint256 i; i < len; ++i) {
204:          id = ids[i];
205:          Credit memory credit = credits[id];
206:          credits[id] = _accrue(credit, id);
207:        }
```

```diff
diff --git a/contracts/modules/credit/LineOfCredit.sol b/contracts/modules/credit/LineOfCredit.sol
index 21ef853..0360364 100644
--- a/contracts/modules/credit/LineOfCredit.sol
+++ b/contracts/modules/credit/LineOfCredit.sol
@@ -200,10 +200,13 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
     function accrueInterest() external override returns(bool) {
         uint256 len = ids.length;
         bytes32 id;
-        for (uint256 i; i < len; ++i) {
+        for (uint256 i; i < len;) {
           id = ids[i];
           Credit memory credit = credits[id];
           credits[id] = _accrue(credit, id);
+          unchecked {
+            ++i;
+          }
         }

         return true;
```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L520-L537
```solidity
File: /contracts/modules/credit/LineOfCredit.sol
        for (uint256 i; i <= lastSpot; ++i) {
          
        }
```

```diff
diff --git a/contracts/modules/credit/LineOfCredit.sol b/contracts/modules/credit/LineOfCredit.sol
index 21ef853..0aca439 100644
--- a/contracts/modules/credit/LineOfCredit.sol
+++ b/contracts/modules/credit/LineOfCredit.sol
@@ -517,7 +517,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
         uint256 lastSpot = ids.length - 1;
         uint256 nextQSpot = lastSpot;
         bytes32 id;
-        for (uint256 i; i <= lastSpot; ++i) {
+        for (uint256 i; i <= lastSpot;) {
             id = ids[i];
             if (p != id) {
                 if (
@@ -533,6 +533,10 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
                 ids[nextQSpot] = p;         // p put at target index
                 return true;
             }
+
+            unchecked {
+                ++i;
+            }

         }
     }
```


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L57-L81
```solidity
File: /contracts/utils/EscrowLib.sol
57:        for (uint256 i; i < length; ++i) {

81:        }
```

```diff
diff --git a/contracts/utils/EscrowLib.sol b/contracts/utils/EscrowLib.sol
index e838d7f..8c945c4 100644
--- a/contracts/utils/EscrowLib.sol
+++ b/contracts/utils/EscrowLib.sol
@@ -54,7 +54,7 @@ library EscrowLib {
         uint256 length = self.collateralTokens.length;
         IOracle o = IOracle(oracle);
         IEscrow.Deposit memory d;
-        for (uint256 i; i < length; ++i) {
+        for (uint256 i; i < length;) {
             address token = self.collateralTokens[i];
             d = self.deposited[token];
              // new var so we don't override original deposit amount for 4626 tokens
@@ -78,6 +78,9 @@ library EscrowLib {
                   d.assetDecimals
                 );
             }
+            unchecked {
+                ++i;
+            }
         }

         return collateralValue;
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L23-L29
```solidity
File: /contracts/utils/CreditListLib.sol
23:      for(uint256 i; i < len; ++i) {
          
29:      }
```

```diff
diff --git a/contracts/utils/CreditListLib.sol b/contracts/utils/CreditListLib.sol
index 1c378e4..28b062a 100644
--- a/contracts/utils/CreditListLib.sol
+++ b/contracts/utils/CreditListLib.sol
@@ -20,12 +20,14 @@ library CreditListLib {
     function removePosition(bytes32[] storage ids, bytes32 id) external returns(bool) {
       uint256 len = ids.length;

-      for(uint256 i; i < len; ++i) {
+      for(uint256 i; i < len;) {
           if(ids[i] == id) {
               delete ids[i];
               return true;
           }
-
+        unchecked {
+          ++i;
+        }
       }

       return true;
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L51-L54
```solidity
File: /contracts/utils/CreditListLib.sol
51:        for(uint i = 1; i < len; ++i) {
52:          ids[i - 1] = ids[i]; // could also clean arr here like in _SortIntoQ
          
54:        }
```

Since the operation `len - 1 ` cannot underflow we can wrap the whole loop with unchecked blocks

```diff
diff --git a/contracts/utils/CreditListLib.sol b/contracts/utils/CreditListLib.sol
index 1c378e4..749dcfc 100644
--- a/contracts/utils/CreditListLib.sol
+++ b/contracts/utils/CreditListLib.sol
@@ -48,12 +48,14 @@ library CreditListLib {
         ids[1] = last;
       } else {
         // move all existing ids up in the queue
-        for(uint i = 1; i < len; ++i) {
-          ids[i - 1] = ids[i]; // could also clean arr here like in _SortIntoQ
+        unchecked {
+          for(uint i = 1; i < len; ++i) {
+            ids[i - 1] = ids[i]; // could also clean arr here like in _SortIntoQ

-        }
+          }
         // cycle first id back to end of queue
-        ids[len - 1] = last;
+         ids[len - 1] = last;
+        }
       }

       return true;
```
[see resource](https://github.com/ethereum/solidity/issues/10695)

### Use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29
```solidity
File: /contracts/modules/interest-rate/InterestRateCredit.sol
26:        require(
27:            msg.sender == lineContract,
28:            "InterestRateCred: only line contract."
29:        );
```

