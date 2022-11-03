## Unspecific Compiler Version Pragma
For most source-units the compiler version pragma is very unspecific ^0.8.9. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Sanity/Threshold/Limit Checks
Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper `uint256` validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values. These checks are generally not implemented in all of the in scope contracts.

## Decimal Used in Constant Assignment
There is no floating point in Solidity. You should keep numbers in whole number format. The following instance of constant assignment should be refactored as follows:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L7

```
    uint256 constant ONE_YEAR = 365 days + 6 hours;
```
## Missing Require Error Message
Consider adding a less than 32 character string message to all require statements just so that a relevant message would be displayed in the event of a revert. The error message is generally not included in all of the in scope contracts.

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.
