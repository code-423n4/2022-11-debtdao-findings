# Debt DAO Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (2 instances)
2. Unspecific Compiler Version Pragma (5 instances)

Total 7 instances of 2 issues.

## 1. Unsafe ERC20 Operation(s) (2 instances)

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

```solidity
contracts/utils/LineLib.sol::48 => payable(receiver).transfer(amount);

contracts/utils/SpigotedLineLib.sol::134 => IERC20(sellToken).approve(swapTarget, amount);
```

## 2. Unspecific Compiler Version Pragma (5 instances)

Avoid floating pragmas for non-library contracts. While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;

contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;

contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;

contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;

contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
```
