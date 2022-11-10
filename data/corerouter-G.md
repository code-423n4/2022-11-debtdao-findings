1. Check whether the owner changes before emitting an event log.

https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotLib.sol#L163-L164

Here the codes do not check whether the owner really changes before making an assignment and emitting an event log. It is suggested to add a checking before these actions as follows to save gas in case the newOwner is the same with previous one:

require(self.owner != newOwner);