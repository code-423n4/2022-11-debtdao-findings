



### [L01] Missing checks for `address(0x0)` when assigning values to `address` state variables


#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::55 => oracle = IOracle(oracle_);
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::56 => arbiter = arbiter_;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::57 => borrower = borrower_;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::206 => credits[id] = _accrue(credit, id);
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::258 => credits[id] = _accrue(credit, id);
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::273 => Credit memory credit = credits[id];
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::274 => credit = _accrue(credit, id);
Line-of-Credit/contracts/modules/escrow/Escrow.sol::48 => minimumCollateralRatio = _minimumCollateralRatio;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::49 => oracle = _oracle;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::50 => borrower = _borrower;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::51 => state.line = _line;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::36 => arbiter = arbiter_;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::37 => oracle = oracle_;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::38 => swapTarget = swapTarget_;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::36 => state.owner = _owner;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::37 => state.operator = _operator;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::38 => state.treasury = _treasury;
Line-of-Credit/contracts/utils/EscrowLib.sol::217 => self.line = _line;
```


### [L02] `address.call{value:x}()` should be used instead of `payable.transfer()`

#### Impact
The use of `payable.transfer()` is heavily frowned upon because it can lead to the locking of funds. The `transfer()` call requires that the recipient has a `payable` callback, only provides 2300 gas for its operation. This means the following cases can cause the transfer to fail:

- The contract does not have a `payable` callback
- The contract’s `payable` callback spends more than 2300 gas (which is only enough to emit something)
- The contract is called through a proxy which itself uses up the 2300 gas

#### Findings:
```
Line-of-Credit/contracts/utils/LineLib.sol::48 => payable(receiver).transfer(amount);
```



### [L03] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
```





#### Non-Critical Issues




### [N01] Adding a return statement when the function defines a named return variable, is redundant


#### Findings:
```
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::68 => return amount;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::128 => return s;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::474 => return credit;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::44 => return s;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::100 => return s;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::132 => return newTokens;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::173 => return newTokens;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::206 => return tokensBought;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::31 => return price;
Line-of-Credit/contracts/utils/CreditLib.sol::160 => return credit;
Line-of-Credit/contracts/utils/CreditLib.sol::180 => return credit;
Line-of-Credit/contracts/utils/CreditLib.sol::193 => return credit;
Line-of-Credit/contracts/utils/CreditLib.sol::225 => return credit;
Line-of-Credit/contracts/utils/CreditLib.sol::229 => return credit;
Line-of-Credit/contracts/utils/CreditLib.sol::261 => return credit;
Line-of-Credit/contracts/utils/EscrowLib.sol::83 => return collateralValue;
Line-of-Credit/contracts/utils/EscrowLib.sol::178 => return cratio;
Line-of-Credit/contracts/utils/SpigotLib.sol::57 => return claimed;
Line-of-Credit/contracts/utils/SpigotLib.sol::101 => return claimed;
Line-of-Credit/contracts/utils/SpigotLib.sol::121 => return claimed;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings


#### Findings:

```

Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::64 => require(escrow_.liquidate(amount, targetToken, to));
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::90 => require(escrow.updateLine(newLine));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::112 => require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::241 => require(interestRate.setRate(id, drate, frate));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::259 => require(interestRate.setRate(id, drate, frate));
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::326 => require(amount <= credit.principal + credit.interestAccrued);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::62 => require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::143 => require(amount <= unusedTokens[credit.token]);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::160 => require(msg.sender == borrower);
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::239 => require(msg.sender == arbiter);
Line-of-Credit/contracts/utils/EscrowLib.sol::91 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::105 => require(msg.sender == ILineOfCredit(self.line).arbiter());
Line-of-Credit/contracts/utils/EscrowLib.sol::161 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::198 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::216 => require(msg.sender == self.line);
Line-of-Credit/contracts/utils/SpigotLib.sol::96 => require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
Line-of-Credit/contracts/utils/SpigotLib.sol::128 => require(revenueContract != address(this));
Line-of-Credit/contracts/utils/SpigotLib.sol::130 => require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
Line-of-Credit/contracts/utils/SpigotLib.sol::155 => require(success);
Line-of-Credit/contracts/utils/SpigotLib.sol::180 => require(newOwner != address(0));
Line-of-Credit/contracts/utils/SpigotLib.sol::189 => require(newOperator != address(0));
Line-of-Credit/contracts/utils/SpigotLib.sol::201 => require(newTreasury != address(0));
Line-of-Credit/contracts/utils/SpigotedLineLib.sol::147 => require(ISpigot(spigot).updateOwner(newLine));
```



### [N03] constants should be defined rather than using magic numbers


#### Findings:
``
Line-of-Credit/contracts/utils/CreditLib.sol::117 => return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
Line-of-Credit/contracts/utils/SpigotLib.sol::90 => uint256 escrowedAmount = claimed * self.settings[revenueContract].ownerSplit / 100;
```



### [N04] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

#### Findings:
```
Line-of-Credit/contracts/utils/MutualConsent.sol::3 => pragma solidity 0.8.9;
```



### [N05] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::31 => uint256 private count; // amount of open credit lines on a Line of Credit facility. ids.length includes null items
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::33 => bytes32[] public ids; // all open credit linest
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::38 => LineLib.STATUS public status;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::32 => EscrowState private state;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::21 => SpigotState private state;

```



### [N06] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::17 => using SafeERC20 for IERC20;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::19 => using CreditListLib for bytes32[];
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::23 => using SafeERC20 for IERC20;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::20 => using SafeERC20 for IERC20;
Line-of-Credit/contracts/modules/escrow/Escrow.sol::21 => using EscrowLib for EscrowState;
Line-of-Credit/contracts/modules/spigot/Spigot.sol::17 => using SpigotLib for SpigotState;
Line-of-Credit/contracts/utils/EscrowLib.sol::22 => using SafeERC20 for IERC20;
Line-of-Credit/contracts/utils/LineLib.sol::15 => using SafeERC20 for IERC20;
```



### [N07] Open TODOs

#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

#### Findings:
```
Line-of-Credit/contracts/modules/factories/LineFactory.sol::140 => // TODO: test
Line-of-Credit/contracts/modules/factories/LineFactory.sol::145 => // TODO: test
```


### [N08] Unused file


#### Findings:

```
Line-of-Credit/contracts/modules/oracle/Oracle.sol::1 => // SPDX-License-Identifier: MIT
```


### [N09] `public` functions not called by the contract should be declared `external` instead

#### Impact

Contracts are allowed to override their parents’ functions and change the visibility from public to external .

#### Findings:

```
Line-of-Credit/contracts/utils/EscrowLib.sol::34 => function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {
Line-of-Credit/contracts/utils/EscrowLib.sol::51 => function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256) {
```



### [N10] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks

#### Findings:

```
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::6 => // 1 Julian astronomical year in seconds to use in calculations for rates = 31557600 seconds
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::7 => uint256 constant ONE_YEAR = 365.25 days;
```



### [N11] Constant redefined elsewhere

#### Impact
when only one location is updated

#### Findings:

```
Line-of-Credit/contracts/utils/CreditLib.sol::69 => return keccak256(abi.encode(line, lender, token));
Line-of-Credit/contracts/utils/MutualConsent.sol::45 => bytes32 expectedHash = keccak256(abi.encodePacked(msg.data, nonCaller));
Line-of-Credit/contracts/utils/MutualConsent.sol::48 => bytes32 newHash = keccak256(abi.encodePacked(msg.data, msg.sender));
```



### [N12] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:

```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
```





