# Debt DAO Gas Optimization Findings
## Summary
The Gas savings are calculated using the `forge test --gas-report` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00      | Use functions instead of modifiers | - | 6 modifiers | not calculated | not calculated |
| G-01      | Save storage variable in memory | - | 1 | - | 160 |
| G-02      | Simplify formula for calculating interest | InterestRateCredit.sol | 1 | 2400 | 344 |
| G-03      | Simplify formula for calculateValue function | CreditLib.sol | 1 | 2203 | 95 |
| G-04      | Use `>` instead of `>=` in healthcheck function | LineOfCredit.sol | 1 | 400 | 3 |

## [G-00] Use functions instead of modifiers
Modifiers make code more elegant and readable but cost more Gas than functions.  
Arguably, the additional Gas cost outweighs the readability benefit.  

There are 6 instances where modifiers are defined.  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L31-L36](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L31-L36)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L78-L81](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L78-L81)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L83-L86](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L83-L86)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L88-L91](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L88-L91)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L98-L103](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L98-L103)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L25-L31](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L25-L31)  

## [G-01] Save storage variable in memory
### self.owner in SpigotLib.claimEscrow function
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L105-L122](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L105-L122)  

Function can be rewritten like this:
```solidity
function claimEscrow(SpigotState storage self, address token)
    external
    returns (uint256 claimed) 
{
    address owner = self.owner;
    if(msg.sender != owner) { revert CallerAccessDenied(); }
    
    claimed = self.escrowed[token];

    if(claimed == 0) { revert ClaimFailed(); }

    LineLib.sendOutTokenOrETH(token, owner, claimed);

    self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?

    emit ClaimEscrow(token, claimed, owner);

    return claimed;
}
```  
Method call Gas saved: **160**

## [G-02] Simplify formula for calculating interest
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L53-L54](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L53-L54)  

The formula for calculating interest in `InterestRateCredit._accrueInterest` currently is:
```solidity
((rate.dRate * drawnBalance * timespan / INTEREST_DENOMINATOR) +
 (rate.fRate * (facilityBalance - drawnBalance) * timespan / INTEREST_DENOMINATOR))
```
This can be simplified to:
```solidity
(rate.dRate * drawnBalance +
 rate.fRate * (facilityBalance - drawnBalance)) * timespan / INTEREST_DENOMINATOR
```
This means one multiplication with `timespan` and division by `INTEREST_DENOMINATOR` can be saved.  

Deployment Gas saved: **2400**  
Method call Gas saved: **344**

## [G-03] Simplify formula for calculateValue function
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L117](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L117)  

The formula for calculating interest in `CreditLib.calculateValue` currently is:
```solidity
price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals)
```

This can be simplified to:
```solidity
price < 1 ? 0 : (amount * uint(price)) / (10 ** decimals)
```

Deployment Gas saved: **2203**  
Method call Gas saved: **95**

## [G-04] Use `>` instead of `>=` in healthcheck function
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L112](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L112)  

Change `require(uint(status) >= uint( LineLib.STATUS.ACTIVE));` t0 `require(uint(status) > uint( LineLib.STATUS.UNINITIALIZED));`.  

Deployment Gas saved: **400**  
Method call Gas saved: **3**