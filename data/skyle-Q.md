## Open TODOS
Consider resolving the TODOs before deploying.
### 
LineFactory.sol line 140
LineFactory.sol line 145

##Use a 2-step ownership transfer pattern
It’s possible that the owner role mistakenly transfers ownership to a wrong address, resulting in a loss of the owner role. Consider first nominating an address as the pendingOwner and implementing an acceptOwnership() function which is called by the pendingOwner to confirm the transfer.

###
Spigot.sol Line 159 updateOwner
Spigot.sol Line 170 updateOperator