# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1] | Using bools for storage incurs overhead | 3 |
| [GAS-2] | Loop can be more optimizable | 6 |
| [GAS-3] | <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 14 |
| [GAS-4] | Same require statement used multiple times | 12 |
| [GAS-5] | “CONSTANTS” expressions are expressions, not CONSTANTS | 2 |
| [GAS-6] | Function that guaranteed to revert when called by normal user can be marked payable  | 10 |
| [GAS-7] | ABI.ENCODE() Is less efficient than ABI.ENCODEPACKED() | 1 |


### [GAS-01] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the  slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

*Instances (3)*:
```solidity
File: contracts/utils/CreditListLib.sol

14:         mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed;

```

```solidity
File: contracts/utils/MutualConsent.sol

15:         mapping(bytes32 => bool) public mutualConsents;

```
```solidity
File: contracts/utils/EscrowLib.sol
16:         mapping(address => bool) enabled;

```



### [GAS-02] Loop can be more optimizable

. Should uncheck ++i


*Instances (6)*:
```solidity
File: contracts/modules/credit/LineOfCredit.sol

179:         for (uint256 i; i < len; ++i) { 
203:         for (uint256 i; i < len; ++i) { 
520:         for (uint256 i; i < len; ++i) { 



```

```solidity
File: contracts/modules/credit/CreditListLib.sol

51:         for (uint256 i; i < len; ++i) { 
23:         for (uint256 i; i < len; ++i) { 

```

```solidity
File: contracts/utils/EscrowLib.sol
57:         for (uint256 i; i < len; ++i) { 
```

### [GAS-03] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

*Instances (14)*:
```solidity
File: contracts/modules/credit/LineOfCredit.sol

192:         principal += _p;
193:         interest += _i;
276:         credit.deposit += amount;
351:         credit.principal += amount;

```

```solidity
File: contracts/modules/credit/SpigotedLine.sol

144:         unusedTokens[credit.token] -= amount;
172:         unusedTokens[targetToken] += newTokens;  

```

```solidity
File: contracts/utils/CreditLib.sol

177:         credit.interestAccrued -= amount;
178:         credit.interestRepaid += amount;
186:         credit.principal -= principalPayment;
187:         credit.interestRepaid += interest;
216:         amount -= interest;
218:         credit.deposit -= amount;  
227:         credit.interestRepaid -= amount;
258:         credit.interestAccrued += accruedToken;  

```

### [GAS-04] Same require statement used multiple times
When same require() condition is repeatedly used in multiple functions, it will more efficient to enclose that require() inside a modifier and use it with functions, This help in deployment gas saving

*Instances (12)*:
```solidity
File: contracts/modules/credit/SpigotedLine.sol

102:         if (msg.sender != borrower && msg.sender != credit.lender) {  
140:         if (msg.sender != borrower && msg.sender != credit.lender) {  

```

```solidity
File: contracts/utils/SpigotLib.sol
62:         if(msg.sender != self.operator) { revert CallerAccessDenied(); }
188:       if(msg.sender != self.operator) { revert CallerAccessDenied(); }


109:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }
126:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }
147:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }
168:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }
179:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }
209:       if(msg.sender != self.owner) { revert CallerAccessDenied(); }

```

```solidity
File: contracts/utils/EscrowLib.sol

199:         require(msg.sender == self.line);
216:         require(msg.sender == self.line);
```


### [GAS-05] “CONSTANTS” expressions are expressions, not CONSTANTS

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Change these expressions from constant to immutable and implement the calculation in the constructor or hardcode these values in the constants and add a comment to say how the value was calculated.

*Instances (2)*:
```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

11:         uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;

```

```solidity
File: contracts/utils/SpigotLib.sol
27:         uint256 constant MAX_REVENUE = type(uint256).max / MAX_SPLIT; 
```

### [GAS-06] Function that guaranteed to revert when called by normal user can be marked payable 

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. 


*Instances (10)*:
```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

34-40:         function accrueInterest( bytes32 id, uint256 drawnBalance, uint256 facilityBalance) external override onlyLineContract returns (uint256) { 

74-86:         function setRate( bytes32 id, uint128 dRate, uint128 fRate ) external onlyLineContract returns (bool) {
```

```solidity
File: contracts/utils/SpigotLib.sol
61:         function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) {
105:       function claimEscrow(SpigotState storage self, address token)
125:       function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {  
143-146: function removeSpigot(SpigotState storage self, address revenueContract)  external returns (bool)
164:       function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
178:       function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
187:       function updateOperator(SpigotState storage self, address newOperator) external returns (bool) { 
208:       function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
```

### [GAS-07] ABI.ENCODE() Is less efficient than ABI.ENCODEPACKED()

*Instances (1)*:
```solidity
File: contracts/utils/SpigotLib.sol

69:         return keccak256(abi.encode(line, lender, token)); 

```





