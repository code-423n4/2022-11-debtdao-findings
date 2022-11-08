## 1. Strings messages in `require` statements should not be longer than 32 bytes of length

_contracts/modules/interest-rate/InterestRateCredit.sol:_ [L26-L29](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29)

## 2. When comparing variables of type `uint`, use `require(x != 0)` instead of `require(x > 0)`

_contracts/modules/credit/LineOfCredit.sol:_ [L398](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L398)
[L484](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L484)
[L526](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L526)

_contracts/utils/EscrowLib.sol:_ [L91](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91)
[L119](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L119)
[L161](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161)
[L198](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198)

## 3. Use `x < y + 1` in stead of `x <= y`

_contracts/modules/credit/SpigotedLine.sol:_ [L62](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62)
[L143](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143)

_contracts/modules/credit/LineOfCredit.sol:_ [L112](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112)
[L132](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132)
[L326](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326)

_contracts/utils/EscrowLib.sol:_ [L127](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L127)

_contracts/utils/CreditLib.sol:_ [L117](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117)
[L136](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L136)
[L176](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L176)

_contracts/utils/CreditListLib.sol:_ [L42](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L42)

## 4. Explicitly assingning default values to variables is a waste of gas

### Use `uint256 i;` instead of `uint256 i = 0;`

_contracts/utils/CreditLib.sol:_ [L188](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L188)
[L219](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L219)

## 5. Calldata is cheaper than memory for function input

_contracts/utils/CreditLib.sol:_ [L74](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L74)
[L203](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L203)
[L240](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L240)

_contracts/modules/spigot/Spigot.sol:_ [L125](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125)

_contracts/utils/SpigotLib.sol:_ [L125](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125)

_contracts/modules/credit/LineOfCredit.sol:_ [L218](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L218)
[L465](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L465)
[L483](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483)

## 6. Custom error are cheaper than string messages

_contracts/modules/interest-rate/InterestRateCredit.sol:_ [L26-L29](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29)

## 7. Use `constant` and `immutable` for constants

_contracts/modules/oracle/Oracle.sol:_ [L14](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L14)

_contracts/modules/credit/LineOfCredit.sol:_ [L38](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L38)

## 8. Consider marking functions as `payable` if there is no risk of sending value through them

### This change will save gas each time a function is called

_contracts/utils/EscrowLib.sol:_ [L104](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L104)
[L215](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L215)
