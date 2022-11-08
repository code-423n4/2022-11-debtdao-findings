# GAS ISSUES FOR [2022-11-DEBTDAO](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03)

## [G-01] A < B + 1 is cheaper than A <= B

./contracts/utils/CreditLib.sol

```
L136:  if(price <= 0 ) { revert NoTokenPrice(); }

L176:  if (amount <= credit.interestAccrued) {
```

./contracts/modules/credit/SpigotedLine.sol

```
L62:   require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

L143:  require(amount <= unusedTokens[credit.token]);
```

./contracts/utils/EscrowLib.sol

```
L127:  if (price <= 0) {
```

./contracts/utils/CreditListLib.sol

```
L42:   if(len <= 1) return true;
```

./contracts/modules/credit/LineOfCredit.sol

```
L112:  require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

L326:  require(amount <= credit.principal + credit.interestAccrued);

L520:  for (uint256 i; i <= lastSpot; ++i) {
```

## [G-02] Consider shortening some string to avoid extra gas

./contracts/modules/interest-rate/InterestRateCredit.sol

```
L26:   require(
L27:   msg.sender == lineContract,
L28:   "InterestRateCred: only line contract."
L29:   );
```

## [G-03] Use custom errors to save gas

./contracts/modules/interest-rate/InterestRateCredit.sol

```
L26:   require(
L27:   msg.sender == lineContract,
L28:   "InterestRateCred: only line contract."
L29:   );
```

## [G-04] `!= 0` comparison is cheaper than `> 0`

./contracts/modules/credit/LineOfCredit.sol

```
L398:  if(facilityFee > 0) {

L484:  if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }

L526:  credits[id].principal > 0
```

./contracts/utils/EscrowLib.sol

```
L91:   require(amount > 0);

L119:  bool is4626 = tokenAddrBytes.length > 0 && passed;

L161:  require(amount > 0);

L198:  require(amount > 0);
```

## [G-05] Use default values of variables types

Description: uint256 - 0;, string - "";, address - address(0);, etc.

./contracts/utils/CreditLib.sol

```
L188:  credit.interestAccrued = 0;

L219:  credit.interestRepaid = 0;
```

## [G-06] Skip copying data from calldata to memory for function parameters

Description: Use `calldata` location for reference-type input args

./contracts/modules/spigot/Spigot.sol

```
L125:  function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
```

./contracts/modules/credit/LineOfCredit.sol

```
       /// @audit Store `credit` in calldata.
L218:  function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {

L465:  function _repay(Credit memory credit, bytes32 id, uint256 amount)

L483:  function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
```

./contracts/utils/CreditLib.sol

```
L169:  ILineOfCredit.Credit memory credit,

L203:  ILineOfCredit.Credit memory credit,

L240:  ILineOfCredit.Credit memory credit,
```

./contracts/utils/SpigotLib.sol

```
L125:  function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```

## [G-07] If a function is not open to any user, it can be marked as `payable` to save gas

Description: Under the hood the compiler checks if a function is payable. The check is skipped if it is marked as such by the developer

./contracts/utils/EscrowLib.sol

```
L104:  function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {

L215:  function updateLine(EscrowState storage self, address _line) external returns(bool) {
```

## [G-08] `uncheck` the `++i` in for loops since there's no way to overflow/underflow

./contracts/utils/EscrowLib.sol

```
L57:   for (uint256 i; i < length; ++i) {
```

./contracts/modules/credit/LineOfCredit.sol

```
L179:  for (uint256 i; i < len; ++i) {

L203:  for (uint256 i; i < len; ++i) {

L520:  for (uint256 i; i <= lastSpot; ++i) {
```

./contracts/utils/CreditListLib.sol

```
L23:   for(uint256 i; i < len; ++i) {

L51:   for(uint i = 1; i < len; ++i) {
```
