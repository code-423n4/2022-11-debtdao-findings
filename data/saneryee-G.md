# Gas Optimization

| |Issue|Instances|
|-|:-|:-:|
|[G-001]|Functions guaranteed to revert when called by normal users can be marked payable|2|
|[G-002]|Use Assembly to check for address(0)|4|
|[G-003]|internal function only called once can be inlined to save gas|1|


## [G-001] Functions guaranteed to revert when called by normal users can be marked payable

### Description:
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

### Findings:
Total:2

[InterestRateCredit.sol#L38](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L38)
```
38:    ) external override onlyLineContract returns (uint256) {
```
[InterestRateCredit.sol#L78](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L78)
```
78:    ) external onlyLineContract returns (bool) {
```

## [G-002] Use Assembly to check for address(0)

### Description:
Saves 6 gas per instance if using assemlby to check for address(0)

### Findings:
Total:4

[LineOfCredit.sol#L445](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L445)
```
445:    if(credits[id].lender != address(0)) { revert PositionExists(); }
```
[SpigotLib.sol#L180](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180)
```
180:    require(newOwner != address(0));
```
[SpigotLib.sol#L189](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189)
```
189:    require(newOperator != address(0));
```
[SpigotLib.sol#L201](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201)
```
201:    require(newTreasury != address(0));
```

## [G-003] internal function only called once can be inlined to save gas

### Description:
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls. 

### Findings:
Total:1

[EscrowedLine.sol#L25](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L25)
```
25:    function _init() internal virtual returns(LineLib.STATUS) {
```