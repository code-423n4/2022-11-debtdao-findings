# RECEIVE() FUNCTION WILL LOCK ETHER IN CONTRACT
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

2 Instances:
-SpigotedLine.sol line 272
-Spigot.sol line 227


## CONSIDER TWO-PHASE OWNERSHIP TRANSFER

Owner can call .updateOwner function to transfer the ownership to the new address directly. As such, there is a risk that the ownership is transferred to an invalid address, thus causing the contract to be without a owner.












