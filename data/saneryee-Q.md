# Low Risk

| |Issue|Instances|
|-|:-|:-:|
|[L-001]|`approve` should be replaced with `safeIncreaseAllowance()` or `safeDecreaseAllowance()`|1|
|[L-002]|Events not emitted for important state changes / Missing event for critical parameter changes|1|


## [L-001] `approve` should be replaced with `safeIncreaseAllowance()` or `safeDecreaseAllowance()`

### Description:
`approve` is subject to a known front-running attack. Consider using `safeIncreaseAllownce()` or `safeDecreaseAllowance()` instead

### Findings:
Total:1

[SpigotedLineLib.sol#L134](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L134)
```
134:    IERC20(sellToken).approve(swapTarget, amount);
```


## [L-002] Events not emitted for important state changes / Missing event for critical parameter changes

### Description:
When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

### Findings:
Total:1

[InterestRateCredit.sol#L74](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L74)
```
74:    function setRate(
```