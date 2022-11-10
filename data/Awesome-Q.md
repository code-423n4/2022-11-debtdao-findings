## Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

For example:
🤦 Bad:

```
pragma solidity ^0.8.0;
```

🚀 Good:

```
pragma solidity 0.8.4;
```

for more info:
[Consensys Audit of 1inch](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)

[Solidity docs](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)

## Typos

Consider sticking to the proper spelling of words otherwise, it will decrease readability

There are 6 instances of this issue
consider making the following changes:

[Line 37](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L37), [Line 45](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L45), [Line 107](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L107), [Line 213](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L213), [Line 355](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L355), [Line 416](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L416), [Line 510](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L510)

```
Line 37:     // Line Financials aggregated accross all existing  Credit

Line 45:     * @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.

Line 107:    * @dev - updates `status` variable in storage if current status is diferent from existing status

Line 213:    @notice - accrues token demoninated interest on a lender's position.

Line 335:    // ensure that borrowing doesnt cause Line to be LIQUIDATABLE

Line 416:    * @notice - updates `status` variable in storage if current status is diferent from existing status.

Line 510:    * @notice - Insert `p` into the next availble FIFO position in the repayment queue
```

Could be refactored to

```
Line 37:     // Line Financials aggregated across all existing  Credit

Line 45:     * @param arbiter_    - A neutral party with some special privileges on behalf of Borrower and Lender.

Line 107:    * @dev - updates `status` variable in storage if current status is different from existing status

Line 213:    @notice - accrues token denominated interest on a lender's position.

Line 335:    // ensure that borrowing doesn't cause Line to be LIQUIDATABLE

Line 416:    * @notice - updates `status` variable in storage if current status is different from existing status.

Line 510:    * @notice - Insert `p` into the next available FIFO position in the repayment queue
```

## Use CamelCase

Consider sticking to using the CamelCase naming convention as it is highly recommended to follow these guidelines to help improve the readability of the source code

here are some instances:

[Line 110](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L110), [Line 121](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L121)

```
Line 110:    function healthcheck() external returns (LineLib.STATUS) {

Line 121:    function _healthcheck() internal virtual returns (LineLib.STATUS) {
```

Could be refactored to

```
Line 110:    function healthCheck() external returns (LineLib.STATUS) {

Line 121:    function _healthCheck() internal virtual returns (LineLib.STATUS) {
```

## Constants should be named with all capital letters

with underscores separating words. (e.g, `MAX_BLOCKS`, `TOKEN_NAME`, `TOKEN_TICKER`, `CONTRACT_VERSION`).
There are 2 instances with this issue:

[Line 12](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12), [Line 14](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L14)

## `block.timestamp` is unreliable

Using block.timestamp as part of the time checks could be slightly modified by miners/validators to favor them in contracts that contain logic heavily dependent on them.

Consider this problem and warn users that a scenario like this could occur. If the change of timestamps will not affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances:
[Line 132](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132), [Line 48-50](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L48-L50), [Line 82](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L82)