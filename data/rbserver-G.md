## [G-01] ARITHMETIC OPERATION THAT DOES NOT OVERFLOW OR UNDERFLOW CAN BE UNCHECKED
Explicitly unchecking the arithmetic operation that does not overflow or underflow by wrapping it in `unchecked {}` costs less gas than implicitly checking it.

`repaid - newTokens` and `newTokens - repaid` can be unchecked because of the `if` and `else` conditions in the following code.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L120-L126
```solidity
        if (repaid > newTokens) {
            // using bought + unused to repay line
            unusedTokens[credit.token] -= repaid - newTokens;
        } else {
            // high revenue and bought more than we need
            unusedTokens[credit.token] += newTokens - repaid;
        }
```

`unusedTokens[credit.token] -= amount` can be unchecked because of the `require` condition in the following code.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143-L144
```solidity
      require(amount <= unusedTokens[credit.token]);
      unusedTokens[credit.token] -= amount;
```

`self.deposited[token].amount -= amount` can be unchecked because of the `if` condition in the following code.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L163-L164
```solidity
        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }
        self.deposited[token].amount -= amount;
```

`self.deposited[token].amount -= amount` can be unchecked because of the `if` condition in the following code.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L200-L202
```solidity
        if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }

        self.deposited[token].amount -= amount;
```

## [G-02] X = X + Y CAN BE USED INSTEAD OF X += Y
x = x + y costs less gas than x += y. For example, `principal += _p` can be changed to `principal = principal + _p` in the following code.

```solidity
contracts\modules\credit\LineOfCredit.sol
  192: principal += _p;
  193: interest += _i;
  276: credit.deposit += amount;
  351: credit.principal += amount;

contracts\modules\credit\SpigotedLine.sol
  125: unusedTokens[credit.token] += newTokens - repaid;
  172: unusedTokens[targetToken] += newTokens;

contracts\utils\CreditLib.sol
  178: credit.interestRepaid += amount;
  187: credit.interestRepaid += interest;
  258: credit.interestAccrued += accruedToken;

contracts\utils\EscrowLib.sol
  75: collateralValue += CreditLib.calculateValue(
  96: self.deposited[token].amount += amount;
```

## [G-03] UNUSED NAMED RETURN COSTS UNNECESSARY DEPLOYMENT GAS
When a function has an unused named return, unnecessary deployment gas is used.

Please consider removing `claimed` in the following function.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L70-L75
```solidity
    function claimRevenue(address revenueContract, address token, bytes calldata data)
        external nonReentrant
        returns (uint256 claimed)
    {
        return state.claimRevenue(revenueContract, token, data);
    }
```

Please consider removing `claimed` in the following function.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L85-L91
```solidity
    function claimEscrow(address token)
        external
        nonReentrant
        returns (uint256 claimed) 
    {
        return state.claimEscrow(token);
    }
```