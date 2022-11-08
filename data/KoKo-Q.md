# QA ISSUES FOR [2022-11-DEBTDAO](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03)

## [L-01] Empty receive and/or fallback function

./contracts/modules/credit/SpigotedLine.sol

```
L272:  receive() external payable {}
```

## [N-01] Use 10e5 instead of 10\*\*5

./contracts/utils/EscrowLib.sol

```
L42:   uint256 _numerator = collateralValue * 10**5;
```
