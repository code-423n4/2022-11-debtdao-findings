# Qa Report

## DEBTDAO

### No SPDX-License-Identifier provided

[InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol):

[LineFactory.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol):

[Spigot.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol):

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

[LineLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol):

[EscrowedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol):

[SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol):

[CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol):

[LineFactoryLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol):

[Escrow.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol):

[EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol):

[SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol):

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

[SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol):

[SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol):

### Do not explicitly `return` from a function if it already has named declared return variables

[CreditLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol):

```solidity
line#L125: function create(
```

[SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol):

```solidity
line#L29:  function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)

line#L83:  function claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)

line#L105: function claimEscrow(SpigotState storage self, address token)
```

[LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol):

```solidity
line#L435: function _createCredit(
```

### Unused receive/fallback function

[SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol):

```solidity
line#L272: receive() external payable {}
```
