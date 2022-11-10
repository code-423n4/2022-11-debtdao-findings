# Gas Optimization Findings

### 1. It is better to increment variable explicitly instead of a shortened version a += b
#### Description
SpigotedLine.sol#L122  
`unusedTokens[credit.token] -= repaid - newTokens;`

SpigotedLine.sol#L125  
`unusedTokens[credit.token] += newTokens - repaid;`

SpigotedLine.sol#L172  
`unusedTokens[targetToken] += newTokens;`

EscrowLib.sol#L96  
`self.deposited[token].amount += amount;`

EscrowLib.sol#L164  
`self.deposited[token].amount -= amount;`
  
EscrowLib.sol#L202  
`self.deposited[token].amount -= amount;`

#### Recommendation
Consider to change expression to this form  
`x = x + y`  

### 2. Uint comparisson
#### Description
When dealing with unsigned integer types, comparisons with `!= 0` are cheaper than with `> 0`.

contracts/modules/credit/LineOfCredit.sol#132  
`if (block.timestamp >= deadline && count > 0) {`

contracts/modules/credit/LineOfCredit.sol#398  
`if(facilityFee > 0) {`

contracts/modules/credit/LineOfCredit.sol#484  
`if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }`

contracts/modules/credit/LineOfCredit.sol#487
`if (credit.deposit + credit.interestRepaid > 0) {`

contracts/modules/credit/LineOfCredit.sol#526
`credits[id].principal > 0 //id should be placed before p`  

/contracts/utils/EscrowLib.sol#91  
`require(amount > 0);`  

/contracts/utils/EscrowLib.sol#119  
`bool is4626 = tokenAddrBytes.length > 0 && passed;`

/contracts/utils/EscrowLib.sol#134  
`if (decimalBytes.length > 0 && successDecimals) {`

/contracts/utils/EscrowLib.sol#161  
`require(amount > 0);`

/contracts/utils/EscrowLib.sol#198  
`require(amount > 0);`

#### Recommendation
Use `not equal zero` instead of `more than zero` comparisson
```
// `a` being of type unsigned integer
require(a > 0, "!a > 0");
```
```
// `a` being of type unsigned integer
require(a != 0, "!a > 0");
```
