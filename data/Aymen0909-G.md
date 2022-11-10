# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Use `unchecked` blocks to save gas  |  3 |
| 2      | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  7 |
| 3      | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops |  5  |

## Findings

### 1- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 3 instances of this issue:

File: contracts/modules/credit/SpigotedLine.sol [Line 144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144)

```
unusedTokens[credit.token] -= amount;
```

The above operation cannot underflow due to the check : [require(amount <= unusedTokens[credit.token]);](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143) and should be modified as follows :

```
unchecked {
    unusedTokens[credit.token] -= amount;
}
```

File: contracts/utils/EscrowLib.sol [Line 164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164)

```
self.deposited[token].amount -= amount;
```

The above operation cannot underflow due to the check : [if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L163) and should be modified as follows :

```
unchecked {
    self.deposited[token].amount -= amount;
}
```

File: contracts/utils/EscrowLib.sol [Line 202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202)

```
self.deposited[token].amount -= amount;
```

The above operation cannot underflow due to the check : [if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L200) and should be modified as follows :

```
unchecked {
    self.deposited[token].amount -= amount;
}
```

### 2- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 7 instances of this issue:

```
File: contracts/modules/credit/SpigotedLine.sol

122     unusedTokens[credit.token] -= repaid - newTokens;
125     unusedTokens[credit.token] += newTokens - repaid;
144     unusedTokens[credit.token] -= amount;
172     unusedTokens[targetToken] += newTokens;

File: contracts/utils/EscrowLib.sol

96      self.deposited[token].amount += amount;
164     self.deposited[token].amount -= amount;
202     self.deposited[token].amount -= amount;
```

### 3- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 5 instances of this issue:

```
File: contracts/modules/credit/LineOfCredit.sol

179     for (uint256 i; i < len; ++i)
203     for (uint256 i; i < len; ++i)
520     for (uint256 i; i <= lastSpot; ++i)

File: contracts/utils/CreditListLib.sol

23      for(uint256 i; i < len; ++i)
51      for(uint i = 1; i < len; ++i) 
```