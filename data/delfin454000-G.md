### Avoid use of default "checked" behavior in a `for` loop
Underflow/overflow checks are made every time `++i` (or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}` instead, as shown below:
___
[LineOfCredit.sol: L179](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L179)
```solidity
        for (uint256 i; i < len; ++i) {
```
Suggestion:
```solidity
        for (uint256 i; i < len;) {

            unchecked {
              ++i;
          }
        }
```
___
Similarly, for the following `for` loops:

[LineOfCredit.sol: L203](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L203)

[LineOfCredit.sol: L520](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L520)

[EscrowLib.sol: L57](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L57)
___
___


### Avoid storage of uints or ints smaller than 32 bytes, if possible
Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed
___
[SpigotedLine.sol: L32](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L32)
```solidity
    uint8 public immutable defaultRevenueSplit;
```
___
[LineFactory.sol: L13](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L13)
```solidity
    uint8 constant MAX_SPLIT = 100; // max % to take
```
___
[LineFactory.sol: L71](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L71)
```solidity
        uint8 split = defaultRevenueSplit; // gas savings
```
___
[SpigotLib.sol: L25](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L25)
```solidity
    uint8 constant MAX_SPLIT = 100;
```
___
[SpigotedLineLib.sol: L13](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L13)
```solidity
    uint8 constant MAX_SPLIT = 100;
```
___
___


### `Require` revert string is too long
The revert string below can be shortened to 32 characters or fewer to save gas. However, more gas could be saved by using a Custom Error
___
[InterestRateCredit.sol: L26-29](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29)
```solidity
        require(
            msg.sender == lineContract,
            "InterestRateCred: only line contract."
        );
```
___
___
