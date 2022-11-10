# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1     | Use `call()` instead of `transfer()` | Low | 1 |
| 2     | Chainlink `latestRoundData()` could return stale prices | Low | 1 |
| 3     | Immutable state variables lack zero address checks | Low | 8 |
| 4     | `require()`/`revert()` statements should have descriptive reason strings | NC | 19 |
| 5     | Duplicated `require()` checks should be refactored to a modifier  | NC |  6 |
| 6     | Named return variables not used anywhere in the functions | NC | 5 |

## Findings

### 1- Use `call()` instead of `transfer()` :

When transferring ETH, `call()` should be used instead of `transfer()`.

The `transfer()` function only allows the recipient to use 2300 gas. If the recipient uses more than that, transfers will fail. In the future gas costs might change increasing the likelihood of that happening.

Keep in mind that `call()` introduces the risk of reentrancy, but as the code follows the CEI pattern it should be fine.

#### Risk : Low

#### Proof of Concept
Instances include:

File: contracts/utils/LineLib.sol [Line 48](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L48)
```
payable(receiver).transfer(amount);
```

#### Mitigation
Replace `transfer()` calls with `call()`, keep in mind to check whether the call was successful by validating the return value.

### 2- Chainlink `latestRoundData()` could return stale prices :

The Chainlink `latestRoundData()` function can sometimes return old prices if the current round is not complete or an old round is used, this can have a negative impact on the protocol lending/borrwing operations as the oracle is used to determine the assets prices when trying to trade or withdraw liquidity.

#### Risk : Low

#### Proof of Concept
Instances include:

File: contracts/modules/oracle/Oracle.sol [Line 22-32](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L22-L32)
```
function getLatestAnswer(address token) external returns (int) {
    (
        /* uint80 roundID */, 
        int price,
        /* uint80 startedAt */,
        /* uint80 timeStamp */,
        /* uint80 answeredInRound */
    ) = registry.latestRoundData(token, Denominations.USD);
    
    return price;
}
```

#### Mitigation

To avoid the risk of getting stale price from Chainlink oracle i recommend to use the `latestRoundData` function with it's required checks.

The functions `getLatestAnswer` should be modified as follow :

```
function getLatestAnswer(address token) external returns (int) {
    (
        uint80 roundID ,
        uint price, 
        uint256 timestamp, 
        uint80 answeredInRound
    ) = registry.latestRoundData(token, Denominations.USD);

    require(price > 0, "Invalid feed price");
    require(answeredInRound >= roundID, "Stale price");
    require(timestamp != 0, "Round not complete");
    
    return price;
}
```

### 3- Immutable state variables lack zero address checks  :

Constructors should check the values written in an immutable state variables(address, uint, int) is not the zero value (address(0) or 0)

#### Risk : Low 

#### Proof of Concept
Instances include:

File: contracts/modules/credit/EscrowedLine.sol [Line 17](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L17)
```
escrow = IEscrow(_escrow);
```

File: contracts/modules/credit/LineOfCredit.sol [Line 55](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L55)
```
oracle = IOracle(oracle_);
```

File: contracts/modules/credit/LineOfCredit.sol [Line 56](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L56)
```
arbiter = arbiter_;
```

File: contracts/modules/credit/LineOfCredit.sol [Line 57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L57)
```
borrower = borrower_;
```

File: contracts/modules/credit/SpigotedLine.sol [Line 64](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L64)
```
spigot = ISpigot(spigot_);
```

File: contracts/modules/credit/SpigotedLine.sol [Line 66](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L66)
```
swapTarget = swapTarget_;
```

File: contracts/modules/escrow/Escrow.sol [Line 49](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L49)
```
oracle = _oracle;
```

File: contracts/modules/escrow/Escrow.sol [Line 50](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L50)
```
borrower = _borrower;
```

#### Mitigation
Add non-zero address checks in the constructors for the instances aforementioned.

### 4- `require()`/`revert()` statements should have descriptive reason strings :

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:
 
```
File: contracts/modules/credit/LineOfCredit.sol

112     require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
241     require(interestRate.setRate(id, drate, frate));
259     require(interestRate.setRate(id, drate, frate));
326     require(amount <= credit.principal + credit.interestAccrued);

File: contracts/modules/credit/SpigotedLine.sol

62      require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
143     require(amount <= unusedTokens[credit.token]);
160     require(msg.sender == borrower);
239     require(msg.sender == arbiter);

File: contracts/utils/EscrowLib.sol

91      require(amount > 0);
105     require(msg.sender == ILineOfCredit(self.line).arbiter());
161     require(amount > 0);
198     require(amount > 0);
216     require(msg.sender == self.line);

File: contracts/utils/SpigotLib.sol

128     require(revenueContract != address(this));
130     require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
155     require(success);
180     require(newOwner != address(0));
189     require(newOperator != address(0));
201     require(newTreasury != address(0));
```

#### Mitigation
Add reason strings to the aforementioned require statements for better comprehension.

### 5- Duplicated `require()/revert()` checks should be refactored to a modifier :

#### Risk : NON CRITICAL

#### Proof of Concept

There are 6 instances of this issue:

```
File: contracts/utils/SpigotLib.sol

109     if(msg.sender != self.owner) { revert CallerAccessDenied(); }     
126     if(msg.sender != self.owner) { revert CallerAccessDenied(); }
147     if(msg.sender != self.owner) { revert CallerAccessDenied(); }
168     if(msg.sender != self.owner) { revert CallerAccessDenied(); }
179     if(msg.sender != self.owner) { revert CallerAccessDenied(); }
209     if(msg.sender != self.owner) { revert CallerAccessDenied(); }
```

#### Mitigation
Those checks should be replaced by a `onlyOwner` modifier as follow :

```
modifier onlyOwner(){
    if(msg.sender != self.owner) { revert CallerAccessDenied(); } 
    _;
}
```

### 6- Named return variables not used anywhere in the function :

When Named return variable are declared they should be used inside the function instead of the return statement or if not they should be removed to avoid confusion.

#### Risk : NON CRITICAL

#### Proof of Concept
Instances include:

File: contracts/utils/CreditLib.sol

[returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L80)

[returns(ILineOfCredit.Credit memory credit)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L133)

File: contracts/utils/SpigotLib.sol

[returns (uint256 claimed)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L31)

[returns (uint256 claimed)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L85)

[returns (uint256 claimed)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L107)

#### Mitigation

Either use the named return variables inplace of the return statement or remove them.