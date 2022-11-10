## Low Severity Report 2022-11-debtdao

### Unspecific Compiler Version Pragma

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

```solidity
pragma solidity ^0.8.9;
```

- [LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1)
- [SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1)
- [SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1)
- [InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L1)
- [Oracle.sol](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L2)