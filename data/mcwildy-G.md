# GAS OPTIMIZATIONS REPORT

## DEBTDAO

### Use x = x + y instead of x += y for storage variables

[SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol):

```solidity
line#L122: unusedTokens[credit.token] -= repaid - newTokens;

line#L125: unusedTokens[credit.token] += newTokens - repaid;

line#L144: unusedTokens[credit.token] -= amount;

line#L172: unusedTokens[targetToken] += newTokens;
```

### `x < y + 1` is cheaper than `x <= y`

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

```solidity
line#L117: return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);

line#L136: if(price <= 0 ) { revert NoTokenPrice(); }

line#L176: if (amount <= credit.interestAccrued) {
```

[CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol):

```solidity
line#L42:  if(len <= 1) return true;
```

[EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol):

```solidity
line#L127: if (price <= 0) {
```

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L112: require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

line#L132: if (block.timestamp >= deadline && count > 0) {

line#L326: require(amount <= credit.principal + credit.interestAccrued);

line#L520: for (uint256 i; i <= lastSpot; ++i) {
```

[SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol):

```solidity
line#L62:  require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

line#L143: require(amount <= unusedTokens[credit.token]);
```

### Use `unchecked` for the counter in `for` loops

Since it is compared to a target value (usually the length of an array) there is no way the counter overflows

[CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol):

```solidity
line#L23:  for(uint256 i; i < len; ++i) {

line#L51:  for(uint i = 1; i < len; ++i) {
```

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L179: for (uint256 i; i < len; ++i) {

line#L203: for (uint256 i; i < len; ++i) {

line#L520: for (uint256 i; i <= lastSpot; ++i) {
```

[EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol):

```solidity
line#L57:  for (uint256 i; i < length; ++i) {
```

### Use `if (x != 0)` instead of `if (x > 0)` for uints comparison

Saves 3 gas per `if`-check

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L398: if(facilityFee > 0) {

line#L484: if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }

line#L526: credits[id].principal > 0
```

[EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol):

```solidity
line#L91:  require(amount > 0);

line#L119: bool is4626 = tokenAddrBytes.length > 0 && passed;

line#L161: require(amount > 0);

line#L198: require(amount > 0);
```

### Do not write default values to variables

If you declare a variables of type `uint256` it will automatically get assigned to its default value of 0. It is a gas wastage to assign it yourself.

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

```solidity
line#L188: credit.interestAccrued = 0;

line#L219: credit.interestRepaid = 0;
```

### Mark functions as `payable` to avoid the low-level evm `is-a-payment-provided` check

The EVM checks whether a payment is provided to the function and this check costs additional gas. If the function is marked as `payable` there is no such check

[EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol):

```solidity
line#L104: function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {

line#L215: function updateLine(EscrowState storage self, address _line) external returns(bool) {
```

### Use `constant` and `immutable` keywords for storage varibles if they doesn't change through contract's lifecycle

That way the variable will go to the contract's bytecode and will not allocate a storage slot

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L38:  LineLib.STATUS public status;
```

[Oracle.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol):

```solidity
line#L14:  FeedRegistryInterface internal registry;
```

### Use `uint256` instead of the smaller uints where possible

The word-size in ethereum is 32 bytes which means that every variables which is sized with less bytes will cost more to operate with

[LineFactory.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol):

```solidity
line#L12:  uint8 constant defaultRevenueSplit = 90;

line#L13:  uint8 constant MAX_SPLIT = 100;

line#L43:  uint32 minCRatio,

line#L71:  uint8 split = defaultRevenueSplit;
```

[SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol):

```solidity
line#L25:  uint8 constant MAX_SPLIT = 100;

line#L164: function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)

line#L252: uint8 indexed split
```

[LineFactoryLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol):

```solidity
line#L13:  uint8 revenueSplit

line#L25:  uint32 indexed minCRatio,

line#L51:  uint8 split = SecuredLine(oldLine).defaultRevenueSplit();

line#L85:  uint8 revenueSplit
```

[SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol):

```solidity
line#L32:  uint8 public immutable defaultRevenueSplit;

line#L60:  uint8 defaultRevenueSplit_
```

[InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol):

```solidity
line#L76:  uint128 dRate,

line#L77:  uint128 fRate
```

[SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol):

```solidity
line#L13:  uint8 constant MAX_SPLIT = 100;

line#L169: function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

line#L170: (uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);
```

[Escrow.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol):

```solidity
line#L24:  uint32 public immutable minimumCollateralRatio;

line#L43:  uint32 _minimumCollateralRatio,
```

[Spigot.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol):

```solidity
line#L146: function updateOwnerSplit(address revenueContract, uint8 ownerSplit)
```

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

```solidity
line#L112: uint8 decimals

line#L138: uint8 decimals;
```

[SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol):

```solidity
line#L21:  uint8 defaultSplit_
```

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L224: uint128 drate,

line#L225: uint128 frate,

line#L249: uint128 drate,

line#L250: uint128 frate
```

### Do not use strings longer than 32 bytes

[InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol):

```solidity
line#L26:  require(
line#L27:  msg.sender == lineContract,
line#L28:  "InterestRateCred: only line contract."
line#L29:  );
```

### Use custom errors instead of `require` strings

[InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol):

```solidity
line#L26:  require(
line#L27:  msg.sender == lineContract,
line#L28:  "InterestRateCred: only line contract."
line#L29:  );
```

### Use `calldata` where possible instead of `memory` to save gas

[SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol):

```solidity
line#L125: function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
           /// @audit Store `credit` in calldata.
line#L218: function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {

line#L465: function _repay(Credit memory credit, bytes32 id, uint256 amount)

line#L483: function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
```

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

```solidity
line#L74:  ILineOfCredit.Credit memory credit,

line#L169: ILineOfCredit.Credit memory credit,

line#L203: ILineOfCredit.Credit memory credit,

line#L240: ILineOfCredit.Credit memory credit,
```

[Spigot.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol):

```solidity
line#L125: function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
```
