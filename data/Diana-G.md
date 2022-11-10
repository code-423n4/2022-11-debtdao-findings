
## G-01 x += y COSTS MORE GAS THAN x = x+y FOR STATE VARIABLES (SAME FOR x -= y)

_There are 20 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

192: principal += _p;
193: interest += _i;
276: credit.deposit += amount;
351: credit.principal += amount;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```
File: contracts/modules/credit/SpigotedLine.sol

122: unusedTokens[credit.token] -= repaid - newTokens;
125: unusedTokens[credit.token] += newTokens - repaid;
144: unusedTokens[credit.token] -= amount;
172: unusedTokens[targetToken] += newTokens;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
File: contracts/utils/CreditLib.sol

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
File: contracts/utils/EscrowLib.sol

75: collateralValue += CreditLib.calculateValue(
96: self.deposited[token].amount += amount;
164: self.deposited[token].amount -= amount;
202: self.deposited[token].amount -= amount;
```

-----------

## G-02 INCREMENTS CAN BE UNCHECKED

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline

Prior to Solidity 0.8.0, arithmetic operations would always wrap in case of under- or overflow leading to widespread use of libraries that introduce additional checks.

Since Solidity 0.8.0, all arithmetic operations revert on over- and underflow by default, thus making the use of these libraries unnecessary.

To obtain the previous behaviour, an unchecked block can be used

_There are 6 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

179: for (uint256 i; i < len; ++i) {
203: for (uint256 i; i < len; ++i) {
520: for (uint256 i; i <= lastSpot; ++i) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol

```
File: contracts/utils/CreditListLib.sol

23: for(uint256 i; i < len; ++i) {
51: for(uint i = 1; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
File: contracts/utils/EscrowLib.sol

57: for (uint256 i; i < length; ++i) {
```

--------------

## G-03 INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

_There are 4 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol

```
File: contracts/modules/credit/EscrowedLine.sol

89: function _rollover(address newLine) internal virtual returns(bool) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

435: function _createCredit(
516: function _sortIntoQ(bytes32 p) internal returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol

```
File: contracts/utils/MutualConsent.sol

65: function _getNonCaller(address _signerOne, address _signerTwo) internal view returns(address) {
```

-----------

## G-04 USAGE OF UINTS OR INTS SMALLER THAN 32 BYTES (26 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

_There are 15 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol

```
File: contracts/modules/credit/SecuredLine.sol

21: uint8 defaultSplit_
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```
File: contracts/modules/credit/SpigotedLine.sol

60: uint8 defaultRevenueSplit_
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol

```
File: contracts/modules/escrow/Escrow.sol

43: uint32 _minimumCollateralRatio,
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol

```
File: contracts/modules/factories/LineFactory.sol

43:  uint32 minCRatio,
71: uint8 split = defaultRevenueSplit;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
File: contracts/utils/CreditLib.sol

112: uint8 decimals
138: uint8 decimals;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol

```
File: contracts/utils/LineFactoryLib.sol

13: uint8 revenueSplit
25: uint32 indexed minCRatio,
51: uint8 split = SecuredLine(oldLine).defaultRevenueSplit();
85: uint8 revenueSplit
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol

```
File: contracts/utils/SpigotedLineLib.sol

169-170: function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {
(uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
File: contracts/utils/SpigotLib.sol

164: unction updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
252: uint8 indexed split
```

------

## G-05 DUPLICATED REQUIRE() OR REVERT() CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

_There are 5 instances of this issue

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

241: require(interestRate.setRate(id, drate, frate));
259: require(interestRate.setRate(id, drate, frate));
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
File: contracts/utils/EscrowLib.sol

91: require(amount > 0);
161: require(amount > 0);
198: require(amount > 0);
```

-----------

## G-06 USING BOTH NAMED RETURNS AND A RETURN STATEMENT ISN'T NECESSARY

Removing unused named returns variables can reduce gas usage (MSTOREs/MLOADs) and improve code clarity. To save gas and improve code quality: consider using only one of those.

_There are 7 instances of this issue

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol

```
File: contracts/utils/Spigot.sol

70-72: function claimRevenue(address revenueContract, address token, bytes calldata data)
        external nonReentrant
        returns (uint256 claimed)
85-88: function claimEscrow(address token)
        external
        nonReentrant
        returns (uint256 claimed)
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```
File: contracts/utils/CreditLib.sol

73-80: function getOutstandingDebt(
    ILineOfCredit.Credit memory credit,
    bytes32 id,
    address oracle,
    address interestRate
  )
    external
    returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)
125-133: function create(
      bytes32 id,
      uint256 amount,
      address lender,
      address token,
      address oracle
  )
      external 
      returns(ILineOfCredit.Credit memory credit)
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
File: contracts/utils/SpigotLib.sol

29-31: function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)
        public
        returns (uint256 claimed)
83-85: function claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)
        external
        returns (uint256 claimed)
105-107: function claimEscrow(SpigotState storage self, address token)
        external
        returns (uint256 claimed)
```

-------

