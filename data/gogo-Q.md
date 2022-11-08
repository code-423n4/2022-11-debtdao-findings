# 2022-11-DEBTDAO

## Low Risk And Non-Critical Issues

### Adding a `return` statement when the function defines a named return variable, is redundant

_There are **5** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

435:  function _createCredit(
453:  return id;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/utils/CreditLib.sol

125:  function create(
160:  return credit;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```solidity
File: contracts/utils/SpigotLib.sol

29:   function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)
57:    return claimed;

83:   function claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)
101:  return claimed;

105:  function claimEscrow(SpigotState storage self, address token)
121:  return claimed;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

### Empty `receive()`/`fallback()` functions

_There are **1** instances of this issue:_

```solidity
File: contracts/modules/credit/SpigotedLine.sol

272:  receive() external payable {}
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

### Use `1e18` instead of `10**18` or `1000000000000000000`

_There are **1** instances of this issue:_

```solidity
File: contracts/utils/EscrowLib.sol

42:     uint256 _numerator = collateralValue * 10**5;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol
