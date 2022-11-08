# 1) No License Identifier indicated: 
Every source file should start with a comment indicating its license:
// SPDX-License-Identifier: MIT

License is indicated nowhere, except for:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol

Recommended mitigation steps:
If you do not want to specify a license or if the source code is not open-source, please use the special value UNLICENSED.


# 2) NatSpec
There are no space after "pragma solidity ^0.8.9";
4 Instances:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol

Recommended mitigation steps:
Consider following NatSpec format by adding spaces between some code lines.


# 3) Different solidity versions:
Most of the contracts have:
pragma solidity ^0.8.9;
Other contracts have exact solidity version (in /contracts/utils/):
pragma solidity 0.8.9;

Recommended mitigation steps:
Consider using 1 Solidity version, or make Solidity version imported from a separate library.


# 4) Deprecated code
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L8-L10


# 5)  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.
2 Instances:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145


# 6) Unnecessary gaps
Apparently, there was some code before, but now there are empty spaces.
5 Instances:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L28
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L53
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L58
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L147
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L222

Recommended mitigation steps:
Consider reformatting your code with some Prettify plugin.


# 7) Group by visibility
Functions should be grouped according to their visibility and ordered:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

In this contract this rule is not followed.
Instance:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol

Recommended mitigation steps:
Consider reordering functions


# 9) Wrong function order
Functions should be ordered following the Solidity conventions.
Inside each contract, library or interface, use the following order:
Type declarations
State variables
Events
Modifiers
Functions

In these contracts events have been placed at the end, after functions:
2 Instances:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L221
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L241

Recommended mitigation steps:
Consider reordering functions


# 10) Typo
revenuw -> revenue
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L188
