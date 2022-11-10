# Gas Optimizations Report for Debt DAO contest
## Overview
During the audit, 3 gas issues were found.  
Total savings ~800.

№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use ```calldata``` instead of ```memory for``` read-only arguments | 5 | 300
G-2 | Use unchecked blocks for incrementing i | 6 | 210
G-3 | Use unchecked blocks for subtractions where underflow is impossible | 9 | 315

## Gas Optimizations Findings(3)
### G-1. Use ```calldata``` instead of ```memory for``` read-only arguments
##### Description
Since Solidity v0.6.9, memory and calldata are allowed in all functions regardless of their visibility type (See "Calldata Variables" section [here](https://blog.soliditylang.org/2020/06/05/Solidity-069-release-announcement/)).  
When function arguments should not be modified, it is cheaper to use calldata.
##### Instances
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L218
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L74
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125

##### Recommendation
Consider using calldata where possible.
##### Saved
This saves at least 60 gas per iteration.  
So, ~60*5 = 300
#
### G-2. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L23
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```
##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*6 = 210
#
### G-3. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- [```repaid - newTokens```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L122)
- [```newTokens - repaid```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L125)
- [```unusedTokens[credit.token] -= amount```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144) 
- [```ids[i - 1] = ids[i]```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L52) 
- [```self.deposited[token].amount -= amount```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164) 
- [```self.deposited[token].amount -= amount```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202) 
- [```claimed - escrowedAmount```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96) 
- [```diff = oldClaimTokens - newClaimTokens;```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L101) 
- [```unused - diff```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L109) 

##### Saved
This saves ~35.  
So, ~35*9 = 315