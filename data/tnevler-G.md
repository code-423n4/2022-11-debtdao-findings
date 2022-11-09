# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i; i < len; ++i) {``` [L179](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179) 
1. ```for (uint256 i; i < len; ++i) {``` [L203](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203) 
1. ```for (uint256 i; i <= lastSpot; ++i) {``` [L520](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520) 
1. ```for(uint256 i; i < len; ++i) {``` [L23](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L23) 
1. ```for(uint i = 1; i < len; ++i) {``` [L51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51) 
1. ```for (uint256 i; i < length; ++i) {``` [L57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```unusedTokens[credit.token] -= repaid - newTokens;``` [L122](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L122) (repaid - newTokens)
1. ```unusedTokens[credit.token] += newTokens - repaid;``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L125) (newTokens - repaid)
1. ```unusedTokens[credit.token] -= amount;``` [L144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144) 
1. ```ids[i - 1] = ids[i]; // could also clean arr here like in _SortIntoQ``` [L52](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L52) (i-1)
1. ```self.deposited[token].amount -= amount;``` [L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164) 
1. ```self.deposited[token].amount -= amount;``` [L202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202) 
1. ```require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));``` [L96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96) (claimed - escrowedAmount)
1. ```uint256 diff = oldClaimTokens - newClaimTokens;``` [L101](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L101) 
1. ```unused - diff``` [L109](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L109) 

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.

### [G-3]: If internal function called only once it can be inlined to save gas
**Context:**

[MutualConsent._getNonCaller](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L65)

**Description:**

If [they](https://blog.soliditylang.org/2021/03/02/saving-gas-with-simple-inliner/) are not inlined, they cost an additional 20 to 40 gas because of 2
extra jump instructions and additional stack operations needed for function calls.

### [G-4]: Cache a value from a mapping/array in local memory
**Context:**

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L34-L49 (self.settings[revenueContract].claimFunction)

**Description:**

If you read value from mapping/array more than once within a function then it is cheaper to cache it in local memory and then read it from memory wnen it is neaded. This will save about 40 gas.

**Recommendation:**

Example how to fix. Change:

```
        if(self.settings[revenueContract].claimFunction == bytes4(0)) {
            // push payments

            // claimed = total balance - already accounted for balance
            claimed = existingBalance - self.escrowed[token];
            // underflow revert ensures we have more tokens than we started with and actually claimed revenue
        } else {
            // pull payments
            if(bytes4(data) != self.settings[revenueContract].claimFunction) { revert BadFunction(); }
            (bool claimSuccess,) = revenueContract.call(data);
            if(!claimSuccess) { revert ClaimFailed(); }

            // claimed = total balance - existing balance
            claimed = LineLib.getBalance(token) - existingBalance;
            // underflow revert ensures we have more tokens than we started with and actually claimed revenue
        }
```

to:

```
        bytes4 _claimFunction = self.settings[revenueContract].claimFunction; 
        if(_claimFunction == bytes4(0)) { 
            // push payments

            // claimed = total balance - already accounted for balance
            claimed = existingBalance - self.escrowed[token];
            // underflow revert ensures we have more tokens than we started with and actually claimed revenue
        } else {
            // pull payments
            if(bytes4(data) != _claimFunction) { revert BadFunction(); }
            (bool claimSuccess,) = revenueContract.call(data);
            if(!claimSuccess) { revert ClaimFailed(); }

            // claimed = total balance - existing balance
            claimed = LineLib.getBalance(token) - existingBalance;
            // underflow revert ensures we have more tokens than we started with and actually claimed revenue
        }
```

### [G-5]: Use calldata instead of memory
**Context:**

1. ```function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {``` [L218](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L218) (credit)
1. ```function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {``` [L483](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483) (credit)
1. ```function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125) (setting)
1. ```ILineOfCredit.Credit memory credit,``` [L74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L74)
1. ```function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125) (setting)

**Description:**

If a reference type function parameter is read-only, it is recommended to use calldata instead of memory because this provides significant gas savings. Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (ie external, public, etc).
