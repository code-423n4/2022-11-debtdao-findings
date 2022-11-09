## [Q-01]

Some functions throughout the contracts are defined as returning a boolean value, however they either revert or always return `true` and the return value is not handled. Some examples are `sendOutTokenOrETH()`, `receiveTokenOrETH()` in `LineLib.sol` and `removePosition()`, `stepQ()` in `CreditListLib.sol`. 

## [Q-02]

`removePosition()` in `CreditListLib.sol` always returns true even if it fails to delete an element in the id's array.

github link: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L20-L33