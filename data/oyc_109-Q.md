## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ISpigot.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ISpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::58 => deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::132 => if (block.timestamp >= deadline && count > 0) {
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::48 => uint256 timespan = block.timestamp - rate.lastAccrued;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::50 => rates[id].lastAccrued = block.timestamp;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::82 => lastAccrued: block.timestamp
```

## [L-03] Unused receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::272 => receive() external payable {}
```

## [L-05] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::38 => function setRate(
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::127 => function setRates(bytes32 id, uint128 drate, uint128 frate) external returns(bool);
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::74 => function setRate(
```

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

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

## [N-02] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::9 => uint256 constant BASE_DENOMINATOR = 10000;
```

## [N-03] require()/revert() statements should have descriptive reason strings

Descriptive reason strings should be used so that users can troubleshot any reverted calls

```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::90 => require(escrow.updateLine(newLine));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::112 => require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::326 => require(amount <= credit.principal + credit.interestAccrued);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::62 => require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::143 => require(amount <= unusedTokens[credit.token]);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::160 => require(msg.sender == borrower);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::239 => require(msg.sender == arbiter);
```

## [N-04] Missing SPDX license identifier

File should include SPDX-License-Identifier

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
Line-of-Credit/contracts/modules/spigot/Spigot.sol::1 => pragma solidity 0.8.9;
```

## [N-05] Missing NatSpec

Code should include NatSpec

```
Line-of-Credit/contracts/interfaces/IEscrow.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IEscrowedLine.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/ILineFactory.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IModuleFactory.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/IOracle.sol::1 => pragma solidity 0.8.9;
Line-of-Credit/contracts/interfaces/ISpigot.sol::1 => pragma solidity ^0.8.9;
```

## [N-07] Adding a return statement when the function defines a named return variable, is redundant

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

## [N-08] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::38 => function setRate(
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::127 => function setRates(bytes32 id, uint128 drate, uint128 frate) external returns(bool);
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::74 => function setRate(
```

## [N-09] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::61 => // Emits that a Borrower has repaid an amount of interest Results in an increase in interestRepaid, i.e. interest not yet withdrawn by a Lender). There is no corresponding function
Line-of-Credit/contracts/interfaces/ISpigotedLine.sol::33 => * @notice - Claims revenue tokens from the Spigot, trades them for credit tokens via a Dex aggregator (Ox protocol) and uses the bought credit tokens to repay debt.
```
