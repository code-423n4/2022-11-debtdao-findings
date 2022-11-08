## 1. Do not leave the `receive`/`fallback` function empty

_contracts/modules/credit/SpigotedLine.sol:_ [L272](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272)
[L273](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L273)

## 2. Use `external` instead of `public` for the following functions

_contracts/utils/CreditLib.sol:_ [L236](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L236)

## 3. Only libraries, abstract contracts and interfaces should use multiple compiler versions

_contracts/modules/credit/SpigotedLine.sol:_ [L1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L1)

_contracts/modules/oracle/Oracle.sol:_ [L2](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L2)

_contracts/modules/credit/SecuredLine.sol:_ [L1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L1)

_contracts/modules/credit/LineOfCredit.sol:_ [L1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L1)

_contracts/modules/interest-rate/InterestRateCredit.sol:_ [L1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L1)

## 4. Use e18 notation instead of \*\*18

_contracts/utils/CreditLib.sol:_ [L117](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117)

_contracts/utils/EscrowLib.sol:_ [L42](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L42)

## 5. Returning named return variables is redundant

_contracts/utils/SpigotLib.sol:_ [L29-L57](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L29-L57)
[L83-L101](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L83-L101)
[L105-L121](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L105-L121)

_contracts/utils/CreditLib.sol:_ [L125-L160](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L125-L160)

_contracts/modules/credit/LineOfCredit.sol:_ [L435-L453](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L435-L453)
