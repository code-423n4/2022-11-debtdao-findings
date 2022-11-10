# Gas optimizations

I only put the instances for which there wasn't a trade off between gas saving for deployment vs methods.

**Optimizer settings:**

```
optimizer = true
optimizer_runs = 200
```

## [G-01] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`

For instance, change this:

```solidity
          Credit memory credit = credits[id];
```

To this:

```solidity
          Credit storage credit = credits[id];
```
---
| Deployment   | $\Delta$ Average gas |
| :----------- | :------------------- |
| LineOfCredit | 1400                 |

| Methods        | $\Delta$ Average gas |
| :------------- | :------------------- |
| accrueInterest | 12                   |
| init()(bool)   | 198                  |
| setRates       | 1                    |

[LineOfCredit.sol#L205](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L205)


```solidity
          Credit memory credit = credits[id];
```

[LineOfCredit.sol#L257](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L257)

```solidity
        Credit memory credit = credits[id];
```

---

| Deployment         | $\Delta$ Average gas |
| :----------------- | :------------------- |
| InterestRateCredit | 8 006                |

| Methods        | $\Delta$ Average gas |
| :------------- | :------------------- |
| accrueInterest | 4                    |

[InterestRateCredit.sol#L47](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L47)

```solidity
        Rate memory rate = rates[id];
```

---

| Methods            | $\Delta$ Average gas |
| :----------------- | :------------------- |
| addCollateral      | 3 681                |
| getCollateralRatio | 939                  |
| getCollateralValue | 7764                 |

[EscrowLib.sol#L56](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L56)

```solidity
        IEscrow.Deposit memory d;
```

## [G-02] State variables should be cached in stack variables rather than re-reading them from storage

| Deployment         | $\Delta$ Average gas |
| :----------------- | :------------------- |
| InterestRateCredit | 3 006                |

| Methods        | $\Delta$ Average gas |
| :------------- | :------------------- |
| accrueInterest | 100                  |

[InterestRateCredit.sol#L47-L50](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L47-L50)

Change this:

```solidity
        Rate memory rate = rates[id];
        uint256 timespan = block.timestamp - rate.lastAccrued;
        // update last timestamp in storage
        rates[id].lastAccrued = block.timestamp;
```

To this (`rate` data location has been set to `storage` in `[G-01]`):

```solidity
        Rate storage rate = rates[id];
        uint256 timespan = block.timestamp - rate.lastAccrued;
        // update last timestamp in storage
        rate.lastAccrued = block.timestamp;
```