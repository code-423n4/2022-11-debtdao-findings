# QA Report: Debt DAO

## Summary

Overall, the code is well-commented. Unit tests are provided. The logic is split into the corresponding files. The logic is clear when referring to the docs/information given in the code4rena contest page.
There is the missing SPDX License Natspec for every file that is in scope. The SPDX natspec is to be added.

## Non-Critical Findings

### NC01: Missing Natspec

**Description**

Code should include the SPDX Natspec at the start of every file.

    // SPDX-License-Identifier: MIT

### NC02: Function State Mutability

**Line References and Details**

[Oracle.sol#L22](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L22)
The state mutability for `getLatestAnswer` can be restricted to `view` as the function only performs read operations on the on-chain data.

### NC03: `Id` Value Already Returned

**Line References and Details**

[LineOfCredit.sol#L453](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L453)
The return statement can be removed as `id` is already returned from the function deinition.

### NC04: Spelling Errors

**Line References and Details**

[InterestRateCredit.sol#L8](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L8)

    too -> to