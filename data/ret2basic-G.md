# Debt DAO Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (5 instances)
2. Use `unchecked{}` to suppress overflow/underflow check (6 instances)
3. Using `bool`s for storage incurs overhead (3 instances)
4. Use `!= 0` instead of `> 0` when comparing uint (10 instances)
5. Empty blocks should be removed or emit something (1 instance)
6. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)
7. Don't compare boolean expressions to boolean literals (1 instance)

Total 27 instances of 7 issues.

## 1. Use `calldata` instead of `memory` (5 instances)

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
contracts/modules/credit/LineOfCredit.sol::218 => function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {

contracts/modules/credit/LineOfCredit.sol::465 => function _repay(Credit memory credit, bytes32 id, uint256 amount)

contracts/modules/credit/LineOfCredit.sol::483 => function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {

contracts/modules/spigot/Spigot.sol::125 => function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {

contracts/utils/SpigotLib.sol::125 => function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```

## 2. Use `unchecked{}` to suppress overflow/underflow check (6 instances)

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
contracts/modules/credit/LineOfCredit.sol::179 => for (uint256 i; i < len; ++i) {

contracts/modules/credit/LineOfCredit.sol::203 => for (uint256 i; i < len; ++i) {

contracts/modules/credit/LineOfCredit.sol::520 => for (uint256 i; i <= lastSpot; ++i) {

contracts/utils/CreditListLib.sol::23 => for(uint256 i; i < len; ++i) {

contracts/utils/CreditListLib.sol::51 => for(uint i = 1; i < len; ++i) {

contracts/utils/EscrowLib.sol::57 => for (uint256 i; i < length; ++i) {
```

## 3. Using `bool`s for storage incurs overhead (3 instances)

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
contracts/modules/credit/SpigotedLine.sol::259 => bool success = SpigotedLineLib.sweep(

contracts/utils/EscrowLib.sol::107 => bool isEnabled = self.enabled[token];

contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
```

## 4. Use `!= 0` instead of `> 0` when comparing uint (10 instances)

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
contracts/modules/credit/LineOfCredit.sol::132 => if (block.timestamp >= deadline && count > 0) {

contracts/modules/credit/LineOfCredit.sol::398 => if(facilityFee > 0) {

contracts/modules/credit/LineOfCredit.sol::484 => if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }

contracts/modules/credit/LineOfCredit.sol::487 => if (credit.deposit + credit.interestRepaid > 0) {

contracts/modules/credit/LineOfCredit.sol::526 => credits[id].principal > 0 //`id` should be placed before `p`

contracts/utils/EscrowLib.sol::91 => require(amount > 0);

contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;

contracts/utils/EscrowLib.sol::134 => if (decimalBytes.length > 0 && successDecimals) {

contracts/utils/EscrowLib.sol::161 => require(amount > 0);

contracts/utils/EscrowLib.sol::198 => require(amount > 0);
```

## 5. Empty blocks should be removed or emit something (1 instance)

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
contracts/modules/credit/SpigotedLine.sol::272 => receive() external payable {}
```

## 6. Use `abi.encodePacked()` instead of `abi.encode()` (1 instance)

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
contracts/utils/CreditLib.sol::69 => return keccak256(abi.encode(line, lender, token));
```

## 7. Don't compare boolean expressions to boolean literals (1 instance)

`if (<x> == true)` can be refactored to `if (<x>)`, `if (<x> == false)` can be refactored to `if (!<x>)`.

```solidity
contracts/modules/credit/LineOfCredit.sol::530 => if(nextQSpot == lastSpot) return true; // nothing to update
```
