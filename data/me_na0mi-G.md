# Gas Optimization Findings

### 1. `x += y` costs more gas than `x = x + y` for state variables
#### Description
SpigotedLine.sol#L122  
```
unusedTokens[credit.token] -= repaid - newTokens;
```

SpigotedLine.sol#L125  
```
unusedTokens[credit.token] += newTokens - repaid;
```

SpigotedLine.sol#L172  
```
unusedTokens[targetToken] += newTokens;
```

EscrowLib.sol#L96  
```
self.deposited[token].amount += amount;
```

EscrowLib.sol#L164  
```
self.deposited[token].amount -= amount;
```
  
EscrowLib.sol#L202  
```
self.deposited[token].amount -= amount;
```

#### Remediation
Consider to change expression to this form  
```
x = x + y
```

#### References
[https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

[https://code4rena.com/reports/2022-09-vtvl/#g05--x--y-costs-more-gas-than-x--x--y-for-state-variables](https://code4rena.com/reports/2022-09-vtvl/#g05--x--y-costs-more-gas-than-x--x--y-for-state-variables)
  
### 2. ++i/i++ should be unchecked when it is possible
#### Description
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas per loop**.

There are several places for gas optimizations:

- LineOfCredit.sol#L179
- LineOfCredit.sol#L203
- LineOfCredit.sol#L520
- EscrowLib.sol#57
#### Remediation
Use unchecked keyword.

#### References

[https://code4rena.com/reports/2022-09-vtvl/#g07--ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops](https://code4rena.com/reports/2022-09-vtvl/#g07--ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for--and-while-loops)

[https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)
  
### 3. Storage arrays should be cached into memory
#### Description
EscrowLib.sol
```
for (uint256 i; i < length; ++i) { //@audit gas optimization
		address token = self.collateralTokens[i]; //@audit gas optimization
    d = self.deposited[token];
		//...
}
```
LineOfCredit.sol#180
```
for (uint256 i; i < len; ++i) {
		id = ids[i]; //@audit gas optimization
		//...
}
```
LineOfCredit.sol#204
```
/// see ILineOfCredit.accrueInterest
    function accrueInterest() external override returns(bool) {
        uint256 len = ids.length;
        bytes32 id;
        for (uint256 i; i < len; ++i) {
          id = ids[i]; //@audit gas optimization
          Credit memory credit = credits[id];
          credits[id] = _accrue(credit, id);
        }
        
        return true;
    }
```
On every iteration of reading an ids storage value contract which costs 2100 because key is cold and evm also reads the lengths of the ids array (which is cost 100 of gas).
 

If the count of open credit lines (ids array length) will be more than 3 caching the ids array in memory is cheaper. If length < 4 creating a copy of ids array is more expensive.

When ids `length==4`  transaction will be cheaper for 23 gas, when `length==10` users will save 491 gas.

#### Remediation
Consider cache array and take len from cached array.

For example:
```
/// see ILineOfCredit.accrueInterest
    function accrueInterest() external override returns(bool) {
        bytes32 id;
				bytes32[] memory idsmem = ids;
        uint256 len = idsmem.length;
        for (uint256 i; i < len; ++i) {
          id = idsmem[i];
          Credit memory credit = credits[id];
          credits[id] = _accrue(credit, id);
        }
        
        return true;
    }
```

### 4. Add unchecked for subtractions where the operands cannot undreflow because of a previous requier of if statament
#### Description
Because of `if (repaid > newTokens) { ` statement it’s possible to use unchecked subtraction to save some amount of gas.

/contracts/modules/credit/SpigotedLine.sol#L122  
```
unusedTokens[credit.token] -= repaid - newTokens;
```
/contracts/modules/credit/SpigotedLine.sol#L125  
```
unusedTokens[credit.token] += newTokens - repaid;
```
/contracts/utils/CreditLib.sol#183
```
uint256 principalPayment = amount - interest;
```
/contracts/utils/SpigotedLineLib.sol#101
```
uint256 diff = oldClaimTokens - newClaimTokens;
```
/contracts/utils/SpigotLib.sol#96
```
require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
```

#### Remediation
```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
```

### 5. Not optimal unsigned integer comparison
#### Description
When dealing with unsigned integer types, comparisons with `!= 0` are cheaper than with `> 0`.

contracts/modules/credit/LineOfCredit.sol#132  
```
if (block.timestamp >= deadline && count > 0) {
```

contracts/modules/credit/LineOfCredit.sol#398  
```
if(facilityFee > 0) {
```

contracts/modules/credit/LineOfCredit.sol#484  
```
if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
```

contracts/modules/credit/LineOfCredit.sol#487
```
if (credit.deposit + credit.interestRepaid > 0) {
```

contracts/modules/credit/LineOfCredit.sol#526
```
credits[id].principal > 0 //id should be placed before p
```

/contracts/utils/EscrowLib.sol#91  
```
require(amount > 0);
```

/contracts/utils/EscrowLib.sol#119  
```
bool is4626 = tokenAddrBytes.length > 0 && passed;
```

/contracts/utils/EscrowLib.sol#134  
```
if (decimalBytes.length > 0 && successDecimals) {
```

/contracts/utils/EscrowLib.sol#161  
```
require(amount > 0);
```

/contracts/utils/EscrowLib.sol#198  
```
require(amount > 0);
```

#### Remediation
```
// `a` being of type unsigned integer
require(a > 0, "!a > 0");
```
```
// `a` being of type unsigned integer
require(a != 0, "!a > 0");
```
