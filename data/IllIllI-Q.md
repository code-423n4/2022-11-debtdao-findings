
## Summary


### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Don't use `payable.transfer()`/`payable.send()` | 1 |
| [L&#x2011;02] | Unused/empty `receive()`/`fallback()` function | 1 |
| [L&#x2011;03] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 |
| [L&#x2011;04] | Open TODOs | 2 |

Total: 9 instances over 4 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Duplicate import statements | 1 |
| [N&#x2011;02] | The `nonReentrant` `modifier` should occur before all other modifiers | 2 |
| [N&#x2011;03] | Contract implements interface without extending the interface | 1 |
| [N&#x2011;04] | Adding a `return` statement when the function defines a named return variable, is redundant | 5 |
| [N&#x2011;05] | `require()`/`revert()` statements should have descriptive reason strings | 23 |
| [N&#x2011;06] | `constant`s should be defined rather than using magic numbers | 7 |
| [N&#x2011;07] | Numeric values having to do with time should use time units for readability | 1 |
| [N&#x2011;08] | Use a more recent version of solidity | 1 |
| [N&#x2011;09] | Use a more recent version of solidity | 6 |
| [N&#x2011;10] | Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`) | 1 |
| [N&#x2011;11] | Constant redefined elsewhere | 5 |
| [N&#x2011;12] | Inconsistent spacing in comments | 2 |
| [N&#x2011;13] | Non-library/interface files should use fixed compiler versions, not floating ones | 5 |
| [N&#x2011;14] | File does not contain an SPDX Identifier | 16 |
| [N&#x2011;15] | NatSpec is incomplete | 56 |
| [N&#x2011;16] | Event is missing `indexed` fields | 4 |
| [N&#x2011;17] | Not using the named return variables anywhere in the function is confusing | 2 |
| [N&#x2011;18] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 2 |

Total: 140 instances over 18 issues



## Low Risk Issues

### [L&#x2011;01]  Don't use `payable.transfer()`/`payable.send()`
The use of `payable.transfer()` is [heavily frowned upon](https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/) because it can lead to the locking of funds. The `transfer()` call requires that the recipient is either an EOA account, or is a contract that has a `payable` callback. For the contract case, the `transfer()` call only provides 2300 gas for the contract to complete its operations. This means the following cases can cause the transfer to fail:
* The contract does not have a `payable` callback
* The contract's `payable` callback spends more than 2300 gas (which is only enough to emit something)
* The contract is called through a proxy which itself uses up the 2300 gas
Use OpenZeppelin's `Address.sendValue()` instead

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/LineLib.sol

48:               payable(receiver).transfer(amount);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48

### [L&#x2011;02]  Unused/empty `receive()`/`fallback()` function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/credit/SpigotedLine.sol

272:      receive() external payable {}

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L272

### [L&#x2011;03]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

56:           arbiter = arbiter_;

57:           borrower = borrower_;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L56

```solidity
File: contracts/modules/credit/SpigotedLine.sol

66:           swapTarget = swapTarget_;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L66

```solidity
File: contracts/modules/escrow/Escrow.sol

49:           oracle = _oracle;

50:           borrower = _borrower;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L49

### [L&#x2011;04]  Open TODOs
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/factories/LineFactory.sol

140:          // TODO: test

145:          // TODO: test

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L140


## Non-critical Issues

### [N&#x2011;01]  Duplicate import statements

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/CreditLib.sol

6:    import { ILineOfCredit } from "../interfaces/ILineOfCredit.sol";

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L6

### [N&#x2011;02]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/credit/SpigotedLine.sol

96:           nonReentrant

157:          nonReentrant

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L96

### [N&#x2011;03]  Contract implements interface without extending the interface
Not extending the interface may lead to the wrong function signature being used, leading to unexpected behavior. If the interface is in fact being implemented, use the `override` keyword to indicate that fact

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/spigot/Spigot.sol

/// @audit IPendleData.treasury()
16:   contract Spigot is ISpigot, ReentrancyGuard {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L16

### [N&#x2011;04]  Adding a `return` statement when the function defines a named return variable, is redundant

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

453:          return id;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L453

```solidity
File: contracts/utils/CreditLib.sol

160:        return credit;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L160

```solidity
File: contracts/utils/SpigotLib.sol

57:           return claimed;

101:          return claimed;

121:          return claimed;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L57

### [N&#x2011;05]  `require()`/`revert()` statements should have descriptive reason strings

*There are 23 instances of this issue:*
```solidity
File: contracts/modules/credit/EscrowedLine.sol

64:       require(escrow_.liquidate(amount, targetToken, to));

90:       require(escrow.updateLine(newLine));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L64

```solidity
File: contracts/modules/credit/LineOfCredit.sol

112:          require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

241:          require(interestRate.setRate(id, drate, frate));

259:          require(interestRate.setRate(id, drate, frate));

326:          require(amount <= credit.principal + credit.interestAccrued);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L112

```solidity
File: contracts/modules/credit/SpigotedLine.sol

62:           require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

143:        require(amount <= unusedTokens[credit.token]);

160:          require(msg.sender == borrower);

239:          require(msg.sender == arbiter);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L62

```solidity
File: contracts/utils/EscrowLib.sol

91:           require(amount > 0);

105:          require(msg.sender == ILineOfCredit(self.line).arbiter());

161:          require(amount > 0);

198:          require(amount > 0);

216:        require(msg.sender == self.line);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L91

```solidity
File: contracts/utils/SpigotedLineLib.sol

147:        require(ISpigot(spigot).updateOwner(newLine));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L147

```solidity
File: contracts/utils/SpigotLib.sol

96:               require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));

128:          require(revenueContract != address(this));

130:          require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));

155:          require(success);

180:          require(newOwner != address(0));

189:          require(newOperator != address(0));

201:          require(newTreasury != address(0));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L96

### [N&#x2011;06]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 7 instances of this issue:*
```solidity
File: contracts/utils/CreditLib.sol

/// @audit 18
140:            decimals = 18;

/// @audit 18
145:            decimals = !passed ? 18 : abi.decode(result, (uint8));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L140

```solidity
File: contracts/utils/EscrowLib.sol

/// @audit 5
42:           uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals

/// @audit 5
43:           return ((_numerator / debtValue) + 5) / 10;

/// @audit 18
113:                  deposit.assetDecimals = 18;

/// @audit 18
137:                      deposit.assetDecimals = 18;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42

```solidity
File: contracts/utils/SpigotLib.sol

/// @audit 100
90:           uint256 escrowedAmount = claimed * self.settings[revenueContract].ownerSplit / 100;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L90

### [N&#x2011;07]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/factories/LineFactory.sol

/// @audit 3000
14:       uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L14

### [N&#x2011;08]  Use a more recent version of solidity
Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/MutualConsent.sol

3:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L3

### [N&#x2011;09]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 6 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1

```solidity
File: contracts/modules/credit/SpigotedLine.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L1

```solidity
File: contracts/modules/escrow/Escrow.sol

1:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L1

```solidity
File: contracts/modules/spigot/Spigot.sol

1:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L1

```solidity
File: contracts/utils/EscrowLib.sol

1:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L1

```solidity
File: contracts/utils/LineLib.sol

1:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L1

### [N&#x2011;10]  Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/EscrowLib.sol

42:           uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42

### [N&#x2011;11]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/escrow/Escrow.sol

/// @audit seen in contracts/modules/credit/LineOfCredit.sol 
27:       address public immutable oracle;

/// @audit seen in contracts/modules/credit/LineOfCredit.sol 
29:       address public immutable borrower;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L27

```solidity
File: contracts/modules/factories/LineFactory.sol

/// @audit seen in contracts/modules/credit/LineOfCredit.sol 
16:       address public immutable arbiter;

/// @audit seen in contracts/modules/escrow/Escrow.sol 
17:       address public immutable oracle;

/// @audit seen in contracts/modules/credit/SpigotedLine.sol 
18:       address public immutable swapTarget;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L16

### [N&#x2011;12]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

58:           deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility

526:                    credits[id].principal > 0 //`id` should be placed before `p` 

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L58

### [N&#x2011;13]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1

```solidity
File: contracts/modules/credit/SecuredLine.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1

```solidity
File: contracts/modules/credit/SpigotedLine.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L1

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

1:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L1

```solidity
File: contracts/modules/oracle/Oracle.sol

2:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L2

### [N&#x2011;14]  File does not contain an SPDX Identifier

*There are 16 instances of this issue:*
```solidity
File: contracts/modules/credit/EscrowedLine.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L0

```solidity
File: contracts/modules/credit/LineOfCredit.sol

0:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L0

```solidity
File: contracts/modules/credit/SecuredLine.sol

0:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L0

```solidity
File: contracts/modules/credit/SpigotedLine.sol

0:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L0

```solidity
File: contracts/modules/escrow/Escrow.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L0

```solidity
File: contracts/modules/factories/LineFactory.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L0

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

0:    pragma solidity ^0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L0

```solidity
File: contracts/modules/spigot/Spigot.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L0

```solidity
File: contracts/utils/CreditLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L0

```solidity
File: contracts/utils/CreditListLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L0

```solidity
File: contracts/utils/EscrowLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L0

```solidity
File: contracts/utils/LineFactoryLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L0

```solidity
File: contracts/utils/LineLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L0

```solidity
File: contracts/utils/MutualConsent.sol

0:    // forked from https://github.com/IndexCoop/index-coop-smart-contracts/blob/master/contracts/lib/MutualConsent.sol

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L0

```solidity
File: contracts/utils/SpigotedLineLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L0

```solidity
File: contracts/utils/SpigotLib.sol

0:    pragma solidity 0.8.9;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L0

### [N&#x2011;15]  NatSpec is incomplete

*There are 56 instances of this issue:*
```solidity
File: contracts/modules/credit/EscrowedLine.sol

/// @audit Missing: '@param newLine'
82      /**
83       * see SecuredlLine.rollover
84       * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
85       *(@dev priviliegad internal function.
86       * @dev MUST only be callable if line is REPAID
87       * @return - if function successfully executed
88      */
89:     function _rollover(address newLine) internal virtual returns(bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L82-L89

```solidity
File: contracts/modules/credit/LineOfCredit.sol

/// @audit Missing: '@return'
216         @param id - the position id for credit position
217       */
218:      function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {

/// @audit Missing: '@param status_'
415       /**
416         * @notice - updates `status` variable in storage if current status is diferent from existing status.
417         * @dev - privileged internal function. MUST check params and logic flow before calling
418         * @dev - does not save new status if it is the same as current status
419         * @return status - the current status of the line after updating
420        */
421:      function _updateStatus(LineLib.STATUS status_) internal returns(LineLib.STATUS) {

/// @audit Missing: '@return'
433        * @param amount - amount of tokens lender will initially deposit
434       */
435       function _createCredit(
436           address lender,
437           address token,
438           uint256 amount
439       )
440           internal
441:          returns (bytes32 id)

/// @audit Missing: '@param credit'
456     /**
457      * @dev - Reduces `principal` and/or `interestAccrued` on a credit line.
458               Expects checks for conditions of repaying and param sanitizing before calling
459               e.g. early repayment of principal, tokens have actually been paid by borrower, etc.
460      * @dev - privileged internal function. MUST check params and logic flow before calling
461      * @param id - position id with all data pertaining to line
462      * @param amount - amount of Credit Token being repaid on credit line
463      * @return credit - position struct in memory with updated values
464     */
465       function _repay(Credit memory credit, bytes32 id, uint256 amount)
466           internal
467:          returns (Credit memory)

/// @audit Missing: '@param credit'
/// @audit Missing: '@param id'
477       /**
478        * @notice - checks that a credit line is fully repaid and removes it
479        * @dev deletes credit storage. Store any data u might need later in call before _close()
480        * @dev - privileged internal function. MUST check params and logic flow before calling
481        * @return credit - position struct in memory with updated values
482        */
483:      function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L216-L218

```solidity
File: contracts/modules/credit/SecuredLine.sol

/// @audit Missing: '@return'
77       * @param targetToken - token in escrow that will be sold of to repay position
78       */
79    
80      function liquidate(
81        uint256 amount,
82        address targetToken
83      )
84        external
85        whileBorrowing
86:       returns(uint256)

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L77-L86

```solidity
File: contracts/modules/escrow/Escrow.sol

/// @audit Missing: '@param _line'
69        /**
70        * @notice - Allows current owner to transfer ownership to another address
71        * @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever
72        * @return didUpdate - if function successfully executed or not
73        */
74:       function updateLine(address _line) external returns(bool) {

/// @audit Missing: '@return'
98         * @param token - the token to all borrow to deposit as collateral
99         */
100:      function enableCollateral(address token) external returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L69-L74

```solidity
File: contracts/modules/oracle/Oracle.sol

/// @audit Missing: '@param token'
19        /**
20         * @return price - the latest price in USD to 8 decimals
21         */
22:       function getLatestAnswer(address token) external returns (int) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L19-L22

```solidity
File: contracts/modules/spigot/Spigot.sol

/// @audit Missing: '@param token'
57        /**
58    
59         * @notice - Claims revenue tokens from the Spigot (push and pull payments) and escrows them for the Owner withdraw later.
60                   - Calls predefined function in contract settings to claim revenue.
61                   - Automatically sends portion to Treasury and then escrows Owner's share
62                   - There is no conversion or trade of revenue tokens. 
63         * @dev    - Assumes the only side effect of calling claimFunc on revenueContract is we receive new tokens.
64                   - Any other side effects could be dangerous to the Spigot or upstream contracts.
65         * @dev    - callable by anyone
66         * @param revenueContract - Contract with registered settings to claim revenue from
67         * @param data - Transaction data, including function signature, to properly claim revenue on revenueContract
68         * @return claimed -  The amount of revenue tokens claimed from revenueContract and split between `owner` and `treasury`
69        */
70        function claimRevenue(address revenueContract, address token, bytes calldata data)
71            external nonReentrant
72:           returns (uint256 claimed)

/// @audit Missing: '@return'
106        * @param data - tx data, including function signature, to call contract with
107        */
108:      function operate(address revenueContract, bytes calldata data) external returns (bool) {

/// @audit Missing: '@return'
123        * @param setting - Spigot settings for smart contract   
124        */
125:      function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {

/// @audit Missing: '@return'
135        * @param revenueContract - smart contract to transfer ownership of
136        */
137       function removeSpigot(address revenueContract)
138           external
139:          returns (bool)

/// @audit Missing: '@return'
157        * @param newOwner - Address to give control to
158        */
159:      function updateOwner(address newOwner) external returns (bool) {

/// @audit Missing: '@return'
168        * @param newOperator - Address to give control to
169        */
170:      function updateOperator(address newOperator) external returns (bool) {

/// @audit Missing: '@return'
179        * @param newTreasury - Address to divert funds to
180        */
181:      function updateTreasury(address newTreasury) external returns (bool) {

/// @audit Missing: '@return'
192        * @param allowed - true/false whether to allow this function to be called by Operator
193        */
194:       function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {

/// @audit Missing: '@return'
204        * @param token Revenue token that is being garnished from spigots
205       */
206:      function getEscrowed(address token) external view returns (uint256) {

/// @audit Missing: '@return'
213        * @param func Function to check on whitelist 
214       */
215   
216:      function isWhitelisted(bytes4 func) external view returns(bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L57-L72

```solidity
File: contracts/utils/CreditLib.sol

/// @audit Missing: '@param id'
/// @audit Missing: '@param amount'
/// @audit Missing: '@param lender'
/// @audit Missing: '@param token'
/// @audit Missing: '@return'
120     /**
121       * see ILineOfCredit._createCredit
122       * @notice called by LineOfCredit._createCredit during every repayment function
123       * @param oracle - interset rate contract used by line that will calculate interest owed
124      */
125     function create(
126         bytes32 id,
127         uint256 amount,
128         address lender,
129         address token,
130         address oracle
131     )
132         external 
133:        returns(ILineOfCredit.Credit memory credit)

/// @audit Missing: '@param id'
/// @audit Missing: '@param amount'
/// @audit Missing: '@return'
163     /**
164       * see ILineOfCredit._repay
165       * @notice called by LineOfCredit._repay during every repayment function
166       * @param credit - The lender position being repaid
167      */
168     function repay(
169       ILineOfCredit.Credit memory credit,
170       bytes32 id,
171       uint256 amount
172     )
173       external
174:      returns (ILineOfCredit.Credit memory)

/// @audit Missing: '@param id'
/// @audit Missing: '@param amount'
/// @audit Missing: '@return'
197     /**
198       * see ILineOfCredit.withdraw
199       * @notice called by LineOfCredit.withdraw during every repayment function
200       * @param credit - The lender position that is being bwithdrawn from
201      */
202     function withdraw(
203       ILineOfCredit.Credit memory credit,
204       bytes32 id,
205       uint256 amount
206     )
207       external
208:      returns (ILineOfCredit.Credit memory)

/// @audit Missing: '@param credit'
/// @audit Missing: '@param id'
/// @audit Missing: '@return'
234     /**
235       * see ILineOfCredit._accrue
236       * @notice called by LineOfCredit._accrue during every repayment function
237       * @param interest - interset rate contract used by line that will calculate interest owed
238      */
239     function accrue(
240       ILineOfCredit.Credit memory credit,
241       bytes32 id,
242       address interest
243     )
244       public
245:      returns (ILineOfCredit.Credit memory)

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L120-L133

```solidity
File: contracts/utils/EscrowLib.sol

/// @audit Missing: '@param self'
28        /**
29         * @notice updates the cratio according to the collateral value vs line value
30         * @dev calls accrue interest on the line contract to update the latest interest payable
31         * @param oracle - address to call for collateral token prices
32         * @return cratio - the updated collateral ratio in 4 decimals
33        */
34:       function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {

/// @audit Missing: '@param self'
46        /**
47        * @notice - Iterates over all enabled tokens and calculates the USD value of all deposited collateral
48        * @param oracle - address to call for collateral token prices
49        * @return totalCollateralValue - the collateral's USD value in 8 decimals
50        */
51:       function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L28-L34

```solidity
File: contracts/utils/LineFactoryLib.sol

/// @audit Missing: '@param oracle'
/// @audit Missing: '@param arbiter'
33        /**
34          @notice sets up new line based of config of old line. Old line does not need to have REPAID status for this call to succeed.
35          @dev borrower must call rollover() on `oldLine` with newly created line address
36          @param oldLine  - line to copy config from for new line.
37          @param borrower - borrower address on new line
38          @param ttl      - set total term length of line
39          @return newLine - address of newly deployed line with oldLine config
40         */
41        function rolloverSecuredLine(
42            address payable oldLine,
43            address borrower, 
44            address oracle,
45            address arbiter,
46            uint ttl
47:       ) external returns(address) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L33-L47

```solidity
File: contracts/utils/LineLib.sol

/// @audit Missing: '@return'
32         * @param amount - amount of tokens to send
33         */
34        function sendOutTokenOrETH(
35          address token,
36          address receiver,
37          uint256 amount
38        )
39          external
40:         returns (bool)

/// @audit Missing: '@return'
57         * @param amount - amount of tokens to send
58         */
59        function receiveTokenOrETH(
60          address token,
61          address sender,
62          uint256 amount
63        )
64          external
65:         returns (bool)

/// @audit Missing: '@return'
78         * @param token - address of token to check. Denominations.ETH for raw ETH
79        */
80:       function getBalance(address token) external view returns (uint256) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L32-L40

```solidity
File: contracts/utils/SpigotedLineLib.sol

/// @audit Missing: '@param spigot'
/// @audit Missing: '@param status'
/// @audit Missing: '@param defaultSplit'
163       /**
164        * @notice Changes the revenue split between a Borrower's treasury and the LineOfCredit based on line health, runs with updateOwnerSplit()
165        * @dev    - callable `arbiter` + `borrower`
166        * @param revenueContract - spigot to update
167        * @return whether or not split was updated
168        */
169:      function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

/// @audit Missing: '@param spigot'
/// @audit Missing: '@param status'
/// @audit Missing: '@param borrower'
/// @audit Missing: '@param arbiter'
/// @audit Missing: '@param to'
186       /**
187   
188      * @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either 
189                   the Borrower (if a Line of Credit has been been fully repaid) or 
190                   to the Arbiter (if the Line of Credit is liquidatable).
191      * @dev    - callable by anyone 
192      * @return - whether or not Spigot was released
193     */
194:      function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {

/// @audit Missing: '@param to'
/// @audit Missing: '@param token'
/// @audit Missing: '@param amount'
/// @audit Missing: '@param status'
/// @audit Missing: '@param borrower'
/// @audit Missing: '@param arbiter'
211     /**
212      * @notice -  Sends any remaining tokens (revenue or credit tokens) in the Spigot to the Borrower after the loan has been repaid.
213                -  In case of a Borrower default (loan status = liquidatable), this is a fallback mechanism to withdraw all the tokens and send them to the Arbiter
214                -  Does not transfer anything if line is healthy
215      * @return - whether or not spigot was released
216     */
217:      function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L163-L169

### [N&#x2011;16]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 4 instances of this issue:*
```solidity
File: contracts/utils/MutualConsent.sol

21        event MutualConsentRegistered(
22            bytes32 _consentHash
23:       );

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L21-L23

```solidity
File: contracts/utils/SpigotLib.sol

241       event AddSpigot(
242           address indexed revenueContract,
243           uint256 ownerSplit
244:      );

255       event ClaimRevenue(
256           address indexed token,
257           uint256 indexed amount,
258           uint256 escrowed,
259           address revenueContract
260:      );

262       event ClaimEscrow(
263           address indexed token,
264           uint256 indexed amount,
265           address owner
266:      );

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L241-L244

### [N&#x2011;17]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/spigot/Spigot.sol

/// @audit claimed
70        function claimRevenue(address revenueContract, address token, bytes calldata data)
71            external nonReentrant
72:           returns (uint256 claimed)

/// @audit claimed
85        function claimEscrow(address token)
86            external
87            nonReentrant
88:           returns (uint256 claimed) 

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L70-L72

### [N&#x2011;18]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

259:          require(interestRate.setRate(id, drate, frate));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L259

```solidity
File: contracts/utils/EscrowLib.sol

161:          require(amount > 0);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L161



_______



## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Return values of `approve()` not checked | 1 |

Total: 1 instances over 1 issues


## Non-critical Issues

### [N&#x2011;01]  Return values of `approve()` not checked
Not all `IERC20` implementations `revert()` when there's a failure in `approve()`. The function signature has a `boolean` return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/SpigotedLineLib.sol

/// @audit (valid but excluded finding)
134:              IERC20(sellToken).approve(swapTarget, amount);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L134

