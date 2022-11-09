# LineOfCredit: ++i should be unchecked
* [LineOfCredit: Line 176](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L179) should be ```for (uint256 i; i < len; unchecked{++i}) {```
* [LineOfCredit: Line 203](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L203) should be ```for (uint256 i; i < len; unchecked{++i}) {```
* [LineOfCredit: Line 520](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L520) should be ```for (uint256 i; i <= lastSpot; unchecked{++i}) {```
* [CreditListLib : Line 23](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L23) should be ```for(uint256 i; i < len; unchecked{++i}) {```
* [CreditListLib : Line 51](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L51) should be ```for(uint i = 1; i < len; unchecked{++i}) {```
* [CreditListLib : Line 57](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L57) should be ```for (uint256 i; i < length; unchecked{++i}) {```

# LineOfCredit#_healthcheck: can replace double comparisson
[These lines](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L124-L127) can be replaced by
```solidity
if (
    uint(s) >= uint(LineLib.STATUS.REPAID)
) {
```
Avoiding one relational operation

# InterestRateCredit#onlyLineContract: Should use custom error to save gas
Repalce [these lines](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29) for:
```solidity
if(msg.sender != lineContract){revert ERROR_ONLY_LINE_CONTRACT();}
```