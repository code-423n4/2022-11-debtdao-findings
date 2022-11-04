
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::31 => uint256 private count; // amount of open credit lines on a Line of Credit facility. ids.length includes null items
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::33 => bytes32[] public ids; // all open credit lines
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::35 => mapping(bytes32 => Credit) public credits; // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::38 => LineLib.STATUS public status;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::32 => EscrowState private state;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::21 => SpigotState private state;
```



### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper
#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::23 => address public immutable borrower;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::25 => address public immutable arbiter;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::27 => address public immutable oracle;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::29 => address public immutable borrower;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::16 => address public immutable arbiter;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::17 => address public immutable oracle;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::18 => address public immutable swapTarget;
```


### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```

Line-of-Credit/contracts/utils/EscrowLib.sol::57 => for (uint256 i; i < length; ++i) {
```



### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::58 => deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::179 => for (uint256 i; i < len; ++i) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::203 => for (uint256 i; i < len; ++i) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::520 => for (uint256 i; i <= lastSpot; ++i) {
Line-of-Credit/contracts/utils/EscrowLib.sol::57 => for (uint256 i; i < length; ++i) {
```


### [G05] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::66 => return _updateStatus(_init());
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::113 => return _updateStatus(_healthcheck());
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::164 => return _updateOutstandingDebt();
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::94 => return _liquidate(ids[0], amount, targetToken, msg.sender);
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::39 => return _accrueInterest(id, drawnBalance, facilityBalance);
Line-of-Credit/contracts/utils/EscrowLib.sol::100 => return _getLatestCollateralRatio(self, oracle);
Line-of-Credit/contracts/utils/EscrowLib.sol::183 => return _getLatestCollateralRatio(self, oracle);
Line-of-Credit/contracts/utils/EscrowLib.sol::188 => return _getCollateralValue(self, oracle);
Line-of-Credit/contracts/utils/EscrowLib.sol::211 => return _getLatestCollateralRatio(self, oracle) < minimumCollateralRatio;
```



### [G06] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement


#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::132 => if (block.timestamp >= deadline && count > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::398 => if(facilityFee > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::484 => if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::487 => if (credit.deposit + credit.interestRepaid > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::526 => credits[id].principal > 0 //`id` should be placed before `p`
Line-of-Credit/contracts/utils/EscrowLib.sol::91 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
Line-of-Credit/contracts/utils/EscrowLib.sol::134 => if (decimalBytes.length > 0 && successDecimals) {
Line-of-Credit/contracts/utils/EscrowLib.sol::161 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::198 => require(amount > 0);
```



### [G07] It costs more gas to initialize variables to zero than to let the default of zero be applied


#### Findings:
```
Line-of-Credit/contracts/utils/CreditLib.sol::188 => credit.interestAccrued = 0;
Line-of-Credit/contracts/utils/CreditLib.sol::219 => credit.interestRepaid = 0;
Line-of-Credit/contracts/utils/SpigotLib.sol::117 => self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?
```


### [G08] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

#### Impact
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detailed description of the issue
#### Findings:
```
Line-of-Credit/contracts/utils/MutualConsent.sol::45 => bytes32 expectedHash = keccak256(abi.encodePacked(msg.data, nonCaller));
Line-of-Credit/contracts/utils/MutualConsent.sol::48 => bytes32 newHash = keccak256(abi.encodePacked(msg.data, msg.sender));
```



### [G09] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```

Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::241 => require(interestRate.setRate(id, drate, frate));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::259 => require(interestRate.setRate(id, drate, frate));

Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::103 => revert CallerAccessDenied();
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::141 => revert CallerAccessDenied();

Line-of-Credit/contracts/modules/factories/LineFactory.sol::28 => revert InvalidArbiterAddress();
Line-of-Credit/contracts/modules/factories/LineFactory.sol::184 => revert InvalidArbiterAddress();

Line-of-Credit/contracts/utils/EscrowLib.sol::91 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::161 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::198 => require(amount > 0);

Line-of-Credit/contracts/utils/SpigotedLineLib.sol::205 => revert CallerAccessDenied();
Line-of-Credit/contracts/utils/SpigotedLineLib.sol::232 => revert CallerAccessDenied();
```



### [G10] `require()` or `revert()` statements that check input arguments should be at the top of the function

#### Impact
Checks that involve constants should come before checks that involve state variables
#### Findings:
```
Line-of-Credit/contracts/utils/SpigotLib.sol::180 => require(newOwner != address(0));
Line-of-Credit/contracts/utils/SpigotLib.sol::189 => require(newOperator != address(0));
Line-of-Credit/contracts/utils/SpigotLib.sol::201 => require(newTreasury != address(0));
```



### [G11] Use a more recent version of solidity

#### Impact
Use a solidity version of at least `0.8.10` to have external calls skip
 contract existence checks if the external call has a return value
 See [original submission](https://github.com/code-423n4/2022-04-phuture-findings/issues/57) for instances.
#### Findings:
```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/CreditLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/CreditListLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/EscrowLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/LineFactoryLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/LineLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/MutualConsent.sol::3 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/SpigotLib.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/utils/SpigotedLineLib.sol::1 => pragma solidity 0.8.9;
```


### [G12] Use `calldata` instead of `memory` for function parameters

#### Impact
Use `calldata` instead of `memory` for function parameters. Having function arguments use calldata instead of memory can save gas.
#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::218 => function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::465 => function _repay(Credit memory credit, bytes32 id, uint256 amount)
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::483 => function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
Line-of-Credit/contracts/modules/spigot/Spigot.sol::125 => function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
Line-of-Credit/contracts/utils/SpigotLib.sol::125 => function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```



### [G13] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:
#### Findings:
```
Line-of-Credit/contracts/utils/LineLib.sol::46 => IERC20(token).safeTransfer(receiver, amount);
Line-of-Credit/contracts/utils/LineLib.sol::48 => payable(receiver).transfer(amount);
```



### [G14] Using `bools` for storage incurs overhead


#### Findings:
```
Line-of-Credit/contracts/utils/EscrowLib.sol::16 => mapping(address => bool) enabled;
Line-of-Credit/contracts/utils/EscrowLib.sol::107 => bool isEnabled = self.enabled[token];
Line-of-Credit/contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
Line-of-Credit/contracts/utils/MutualConsent.sol::15 => mapping(bytes32 => bool) public mutualConsents;
Line-of-Credit/contracts/utils/SpigotLib.sol::14 => mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
```



### [G15] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...} `=> `if(!x){if(y){...}else{...}}`)
#### Findings:
```
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::272 => receive() external payable {}
```

### [G16] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
Line-of-Credit/contracts/utils/CreditLib.sol::69 => return keccak256(abi.encode(line, lender, token));
```



### [G17] Optimize names to save gas

#### Impact
`public`/`external` function names and public member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)
#### Findings:
```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::12 => abstract contract EscrowedLine is IEscrowedLine, ILineOfCredit {
Line-of-Credit/contracts/utils/MutualConsent.sol::11 => abstract contract MutualConsent {
```






### [G18] <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES


#### Findings:
```
Line-of-Credit/contracts/utils/LineOfCredit.sol::192 => principal += _p;
Line-of-Credit/contracts/utils/LineOfCredit.sol::193 => interest += _i;
Line-of-Credit/contracts/utils/LineOfCredit.sol::276 => credit.deposit += amount;
Line-of-Credit/contracts/utils/LineOfCredit.sol::351 => credit.principal += amount;
Line-of-Credit/contracts/utils/SpigotedLine.sol::122 => unusedTokens[credit.token] -= repaid - newTokens;
Line-of-Credit/contracts/utils/SpigotedLine.sol::125 => unusedTokens[credit.token] += newTokens - repaid;
Line-of-Credit/contracts/utils/SpigotedLine.sol::144 => unusedTokens[credit.token] -= amount;
Line-of-Credit/contracts/utils/SpigotedLine.sol::172 => unusedTokens[targetToken] += newTokens;
Line-of-Credit/contracts/utils/CreditLib.sol::177 => credit.interestAccrued -= amount;
Line-of-Credit/contracts/utils/CreditLib.sol::178 => credit.interestRepaid += amount;
Line-of-Credit/contracts/utils/CreditLib.sol::186 => credit.principal -= principalPayment;
Line-of-Credit/contracts/utils/CreditLib.sol::187 => credit.interestRepaid += interest;
Line-of-Credit/contracts/utils/CreditLib.sol::216 => amount -= interest;
Line-of-Credit/contracts/utils/CreditLib.sol::218 => credit.deposit -= amount;
Line-of-Credit/contracts/utils/CreditLib.sol::227 => credit.interestRepaid -= amount;
Line-of-Credit/contracts/utils/CreditLib.sol::258 => credit.interestAccrued += accruedToken;
Line-of-Credit/contracts/utils/EscrowLib.sol::164 => self.deposited[token].amount -= amount;
Line-of-Credit/contracts/utils/EscrowLib.sol::202 => self.deposited[token].amount -= amount;
Line-of-Credit/contracts/utils/EscrowLib.sol::75 => collateralValue += CreditLib.calculateValue(
Line-of-Credit/contracts/utils/EscrowLib.sol::96 => self.deposited[token].amount += amount;
```



### [G19] SAVE GAS BY NOT REQURING NON-ZERO INTERVAL IF NO LINEAR AMOUNT

#### Impact
If there is no linear amount, a Gsset for the claim’s interval can be converted to a Gsreset, saving 17100 gas.

#### Findings:
```
Line-of-Credit/contracts/utils/EscrowLib.sol::91 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::161 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::198 => require(amount > 0);
```








### [G20] STORAGE POINTER TO A STRUCTURE IS CHEAPER THAN COPYING EACH VALUE OF THE STRUCTURE INTO MEMORY, SAME FOR ARRAY AND MAPPING 

#### Impact
Deployment Gas Saved: 168 820
Method Call Gas Saved: 1 672
forge snapshot --diff: 8 746 Gas Saved

It may not be obvious, but every time you copy a storage struct/array/mapping to a memory variable, you are copying each member by reading it from storage, which is expensive. And when you use the storage keyword, you are just storing a pointer to the storage, which is much cheaper. Exception: case when you need to read all or many members multiple times. In report included only cases that saved gas

#### Findings:
```
Line-of-Credit/contracts/utils/LineOfCredit.sol::205 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/LineOfCredit.sol::257 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/LineOfCredit.sol::273 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/LineOfCredit.sol::301 => Credit memory credit = _accrue(credits[id], id);
Line-of-Credit/contracts/utils/LineOfCredit.sol::323 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/LineOfCredit.sol::347 => Credit memory credit = _accrue(credits[id], id);
Line-of-Credit/contracts/utils/LineOfCredit.sol::375 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/LineOfCredit.sol::389 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/SpigotedLine.sol::100 => Credit memory credit = _accrue(credits[id], id);
Line-of-Credit/contracts/utils/SpigotedLine.sol::139 => Credit memory credit = credits[id];
Line-of-Credit/contracts/utils/InterestRateCredit.sol::47 => Rate memory rate = rates[id];
Line-of-Credit/contracts/utils/EscrowLib.sol::108 => IEscrow.Deposit memory deposit = self.deposited[token];
```








### [G21] REPLACE MODIFIER WITH FUNCTION 

#### Impact
Modifiers make code more elegant, but cost more than normal functions.

Deployment Gas Saved: 460 154

All modifiers except permissioned due to unresolved error flow.
#### Findings:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L78-103

```

      '    modifier whileActive() {
        if(status != LineLib.STATUS.ACTIVE) { revert NotActive(); }
        _;
    }

    modifier whileBorrowing() {
        if(count == 0 || credits[ids[0]].principal == 0) { revert NotBorrowing(); }
        _;
    }

    modifier onlyBorrower() {
        if(msg.sender != borrower) { revert CallerAccessDenied(); }
        _;
    }

    /**
     * @notice - mutualConsent() but hardcodes borrower address and uses the position id to
                 get Lender address instead of passing it in directly
     * @param id - position to pull lender address from for mutual consent agreement
    */
    modifier mutualConsentById(bytes32 id) {
      if(_mutualConsent(borrower, credits[id].lender))  {
        // Run whatever code is needed for the 2/2 consent
        _;
      }
    }
'

```



