# <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES
Using the addition operator instead of plus-equals saves 113 gas.

There are 8 instances of this issue:

```
File: contracts/modules/credit/LineOfCredit.sol

192:               principal += _p;
193:               interest += _i;
276:               credit.deposit += amount;
351:               credit.principal += amount;
```
```
File: contracts/modules/credit/SpigotedLine.sol

122:               unusedTokens[credit.token] -= repaid - newTokens;
125:               unusedTokens[credit.token] += newTokens - repaid;
144:               unusedTokens[credit.token] -= amount;
172:               unusedTokens[targetToken] += newTokens;
```