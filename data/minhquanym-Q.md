# Summary

| Id | Title |
| -- | ----- |
| 1 | Not support fee-on-transfer tokens  |
| 2 | Lender call `close()` will lose ETH |
| 3 | Implementation mismatch documentation in `updateSplit()` function |
| 4 | Unsafe cast from `bytes` to `bytes4` in function `operate()` |
| 5 | Cannot cancel mutual consent |

## 1. Not support fee-on-transfer tokens

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L223

Every tokens of credit line will be transferred from lender to LineOfCredit first, then to borrower later. These 2-transfer steps will make the tax for some fee-on-transfer tokens accounted twice. 

However, current logic is not handled it. So if lender uses fee-on-transfer tokens, contract will not have enough balance to transfer to borrower.

```solidity
function addCredit(
    uint128 drate,
    uint128 frate,
    uint256 amount,
    address token,
    address lender
)
    external
    payable
    override
    whileActive
    mutualConsent(lender, borrower) // @note Lender call first will lose ETH
    returns (bytes32)
{
    LineLib.receiveTokenOrETH(token, lender, amount);

    bytes32 id = _createCredit(lender, token, amount);

    require(interestRate.setRate(id, drate, frate));

    return id;
}
```


### Recommendation
Consider comparing pre and after balance when receiving token.

## 2. Lender call `close()` will lose ETH

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L388

Function `LineOfCredit.close()` allowed both borrower and lender of the credit line to call. However, if credit token is native token, lender call this function will lose ETH but there is no check for that.

### Recommendation
Consider preventing lender from calling `close()` if credit token is ETH


## 3. Implementation mismatch documentation in `updateSplit()` function

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L165

In the NatSpec comment of function `updateSplit()`, it said that this function is called by arbiter and borrower only. However, there is no check for that in the implementation, anyone can call this function.

However I did not found any risk here since the values to be updated are fixed (`split = defaultSplit / MAX_SPLIT`) 

### Recommendation
Consider updating the document.

## 4. Unsafe cast from `bytes` to `bytes4` in function `operate()`

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L65

In `operate()` function, `bytes data` is unsafe casted to `bytes4` to get the function selector. However, this unsafe cast can lead to unexpected behaviour when providing wrong data.

If `bytes data` is less than 4 bytes, it will pad `0` at the end when casting to `bytes4`. And if it is occasionally identical to another whitelisted function, it will make the call to unwhitelisted function possible (`fallback()`, `receive()`) and lead to unexpected behaviour.

### Recommendation
Consider using SafeCast library

## 5. Cannot cancel mutual consent

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L11

Mutual consent works by using two TXs with the same `msg.data`. However, when first one call, there is no way to cancel it. First caller might send wrong `msg.data` or later caller change the mind in the midway. 

Since it's not possible to cancel the process, later caller can take benefit and call it in the future. 

### Recommendation
Consider allowing to cancel the mutual consent process after some time interval.
