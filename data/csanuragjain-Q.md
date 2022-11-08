## User funds may get lost

Contract:
[SpigotedLineLib.sol#L131](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L131)

Function:
[trade](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L120)

Issue:
1. If User initiated a trade with zeroExTradeData's amount as X where claimToken was ETH and total amount of claimToken which were considered for this swap was X+A then A amount would be lost in 0X contract

POC:
1. [trade](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L120) function was called with amount as X+A and zeroExTradeData has swap amount as X

2. This causes contract to send amount X+A to 0x contract. Since zeroExTradeData only mention swap of X amount so only X amount is swapped and returned to our contract. Remaining amount A is locked in 0x contract

Recommendation:
Update the documentation to make user aware about such risks

## Incorrect event

Contract:
[ModuleFactory.sol#L28](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/ModuleFactory.sol#L28)

Function:
[deployEscrow](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/ModuleFactory.sol#L26)

Issue:
As per [IModuleFactory.sol#L12](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/interfaces/IModuleFactory.sol#L12) the third param in DeployedEscrow should be oracle and not borrower

Recommendation:
Kindly revise the deployEscrow function as shown below:

```solidity
emit DeployedEscrow(module, minCRatio, oracle, owner);
```