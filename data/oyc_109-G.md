## [G-01] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
Line-of-Credit/contracts/interfaces/ISpigot.sol::57 => function addSpigot(address revenueContract, Setting memory setting) external returns (bool);
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::218 => function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::483 => function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
Line-of-Credit/contracts/modules/spigot/Spigot.sol::125 => function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
```

## [G-02] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::14 => IEscrow immutable public escrow;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::21 => uint256 public immutable deadline;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::23 => address public immutable borrower;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::25 => address public immutable arbiter;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::27 => IOracle public immutable oracle;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::29 => InterestRateCredit public immutable interestRate;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::26 => ISpigot public immutable spigot;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::29 => address payable public immutable swapTarget;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::32 => uint8 public immutable defaultRevenueSplit;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::24 => uint32 public immutable minimumCollateralRatio;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::27 => address public immutable oracle;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::29 => address public immutable borrower;
```

## [G-03] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::272 => receive() external payable {}
```

## [G-04] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
Line-of-Credit/contracts/interfaces/IEscrow.sol::8 => uint8 assetDecimals;
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::7 => uint128 dRate;
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::10 => uint128 fRate;
Line-of-Credit/contracts/interfaces/ILineFactory.sol::7 => uint32 cratio;
Line-of-Credit/contracts/interfaces/ILineFactory.sol::8 => uint8 revenueSplit;
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::14 => uint8 decimals;           // Decimals of Credit Token for calcs
Line-of-Credit/contracts/interfaces/ISpigot.sol::6 => uint8 ownerSplit;             // x/100 % to Owner, rest to Treasury
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::32 => uint8 public immutable defaultRevenueSplit;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::24 => uint32 public immutable minimumCollateralRatio;
```

## [G-05] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::179 => for (uint256 i; i < len; ++i) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::203 => for (uint256 i; i < len; ++i) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::520 => for (uint256 i; i <= lastSpot; ++i) {
```

## [G-06] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::192 => principal += _p;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::193 => interest += _i;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::276 => credit.deposit += amount;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::351 => credit.principal += amount;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::122 => unusedTokens[credit.token] -= repaid - newTokens;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::125 => unusedTokens[credit.token] += newTokens - repaid;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::144 => unusedTokens[credit.token] -= amount;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::172 => unusedTokens[targetToken] += newTokens;
```

## [G-07] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::11 => uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;
```

## [G-08] Use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
Line-of-Credit/contracts/interfaces/IEscrow.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IEscrowedLine.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ILineFactory.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IModuleFactory.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IOracle.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/ISecuredLine.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/ISpigot.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ISpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::1 => pragma solidity 0.8.9;
```

## [G-09] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::117 => function counts() external view returns (uint256, uint256) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::163 => function updateOutstandingDebt() external override returns (uint256, uint256) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::169 => returns (uint256 principal, uint256 interest)
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::441 => returns (bytes32 id)
Line-of-Credit/contracts/modules/spigot/Spigot.sol::72 => returns (uint256 claimed)
Line-of-Credit/contracts/modules/spigot/Spigot.sol::88 => returns (uint256 claimed)
Line-of-Credit/contracts/modules/spigot/Spigot.sol::222 => returns(uint8, bytes4, bytes4)
```

## [G-10] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::445 => if(credits[id].lender != address(0)) { revert PositionExists(); }
```

## [G-12] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::25 => function _init() internal virtual returns(LineLib.STATUS) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::34 => function _healthcheck() virtual internal returns(LineLib.STATUS) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::77 => function _canDeclareInsolvent() internal virtual returns(bool) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::89 => function _rollover(address newLine) internal virtual returns(bool) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::69 => function _init() internal virtual returns(LineLib.STATUS) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::157 => function _canDeclareInsolvent() internal virtual returns(bool) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::516 => function _sortIntoQ(bytes32 p) internal returns (bool) {
```

## [G-14] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::25 => function _init() internal virtual returns(LineLib.STATUS) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::34 => function _healthcheck() virtual internal returns(LineLib.STATUS) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::77 => function _canDeclareInsolvent() internal virtual returns(bool) {
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::89 => function _rollover(address newLine) internal virtual returns(bool) {
```