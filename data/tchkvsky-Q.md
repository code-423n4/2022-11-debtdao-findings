Unlocked pragma

### Handle

tchkvsky

## Scope

[LineOfCredit.sol #L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1)

[SecuredLine.sol #L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1)

[SpigotedLine.sol #L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L1)

[InterestRateCredit.sol #L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L1)

[Oracle.sol #L2](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L2)

### Vulnerability details

Avoid floating praga for non-library contracts. Locking the pragma ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.

### Recommendation

Lock pragma to a specific compiler version.   

----

No license identifier

## Handle

Tchkvsky

### Vulnerability details

Solidity source files are recommended to start with a comment indicating its license, where the compiler includes the supplied string in the bytecode metadata to make it machine readable. This issue is seen in all contracts in scope except `Oracle.sol`.

### Recommendation

Include an SPDX license identifier