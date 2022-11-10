### G-01 ++I or I++ SHOULD BE UNCHECKED{++I} or UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

*Number of Instances Identified: 6*

the `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
179: for (uint256 i; i < len; ++i) {
203: for (uint256 i; i < len; ++i) {
520: for (uint256 i; i <= lastSpot; ++i) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol

```
23: for(uint256 i; i < len; ++i) {
51: for(uint i = 1; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
57: for (uint256 i; i < length; ++i) {
```


### G-02 X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES


*Number of Instances Identified: 20*

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
192: principal += _p;
193: interest += _i;
276: credit.deposit += amount;
351: credit.principal += amount;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```
122: unusedTokens[credit.token] -= repaid - newTokens;
125: unusedTokens[credit.token] += newTokens - repaid;
144: unusedTokens[credit.token] -= amount;
172: unusedTokens[targetToken] += newTokens;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
177: credit.interestAccrued -= amount;
178: credit.interestRepaid += amount;
186: credit.principal -= principalPayment;
187: credit.interestRepaid += interest;
216: amount -= interest;
218: credit.deposit -= amount;
227: credit.interestRepaid -= amount;
258: credit.interestAccrued += accruedToken;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
75: collateralValue += CreditLib.calculateValue(
96: self.deposited[token].amount += amount;
164: self.deposited[token].amount -= amount;
202: self.deposited[token].amount -= amount;
```

### G-03 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

*Number of Instances Identified: 3*

Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.


https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol

```
89: function _rollover(address newLine) internal virtual returns(bool)
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
435: function _createCredit
516: function _sortIntoQ(bytes32 p) internal returns (bool)
```


### G-04 USING BOOLS FOR STORAGE INCURS OVERHEAD

*Number of Instances Identified: 16*

```
    // Booleans are more expensive than uint256 or any type that takes up a full    // word because each write operation emits an extra SLOAD to first read the    // slot's contents, replace the bits taken up by the boolean, and then write    // back. This is the compiler's defense against contract upgrades and    // pointer aliasing, and it cannot be disabled.
```

[https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
142: (bool passed, bytes memory result) = token.call(
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
65: (bool success, bytes memory assetAmount) = token.call(
107: bool isEnabled = self.enabled[token];
115: (bool passed, bytes memory tokenAddrBytes) = token.call(
119: bool is4626 = tokenAddrBytes.length > 0 && passed;
131: (bool successDecimals, bytes memory decimalBytes) = deposit
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol

```
58: (bool success, bytes memory returnVal) = spigot.call(
62: (bool success2, bytes memory returnVal2) = escrow.call(
66: (bool res) = abi.decode(returnVal, (bool));
67: (bool res2) = abi.decode(returnVal2, (bool));
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol

```
131: (bool success, ) = swapTarget.call{value: amount}(zeroExTradeData);
135: (bool success, ) = swapTarget.call(zeroExTradeData);
259: bool success
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
43: (bool claimSuccess,) = revenueContract.call(data);
76: (bool success,) = revenueContract.call(data);
149: (bool success,) = revenueContract.call(
```


### G-05 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISNT NECESSARY

*Number of Instances Identified: 7*

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol

```
72: returns (uint256 claimed)
88: returns (uint256 claimed)
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
80: returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)
133: returns(ILineOfCredit.Credit memory credit)
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
31: returns (uint256 claimed)
85: returns (uint256 claimed)
107: returns (uint256 claimed)
```


### G-06 DUPLICATED REQUIRE() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

*Number of Instances Identified: 2*

Saves deployment costs


https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
91: require(amount > 0);
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
241: require(interestRate.setRate(id, drate, frate));
```


### G-07 USAGE OF UINTS or INTS SMALLER THAN 32 BYTES INCURS OVERHEAD

*Number of Instances Identified: 7*

> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol

```
21: uint8 defaultSplit_
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```
60: uint8 defaultRevenueSplit_
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol

```
43: uint32 _minimumCollateralRatio,
```


https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol

```
43: uint32 minCRatio,
71: uint8 split = defaultRevenueSplit;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
112: uint8 decimals
138: uint8 decimals;
```
