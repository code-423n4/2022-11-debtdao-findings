# LineOfCredit#constructor: Lack of zero addess check
Next state variable can be set to zero:

Mitigation steps: Add next lines inside constructor:
```solidity
if (oracle_ == address(0) || arbiter_ == address(0) || borrower_ = address(0)){revert ERROR_ZERO_ADDRESS();} // Create error ERROR_ZERO_ADDRESS()
```

It must be said that if arbitrer is equal to zero address, then function ```declareInsolvent``` would be uncallable


# SpigotLib#updateOwnerSplit can emmit redundant event
[It does not check if the previous owner split is equal the the new owner split](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L164-L175), leading to redundant event emission. The function should be changed to:

```solidity
function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
    external
    returns(bool)
{
    if(msg.sender != self.owner) { revert CallerAccessDenied(); }
    if(ownerSplit > MAX_SPLIT) { revert BadSetting(); }
    require(self.settings[revenueContract].ownerSplit != ownerSplit);

    self.settings[revenueContract].ownerSplit = ownerSplit;
    emit UpdateOwnerSplit(revenueContract, ownerSplit);
    
    return true;
}
```

# SpigotLib owner change should be done in to steps
To avoid mistakenly transfer the owner role to a wrong address, it would be recommendable to divide the owner role transfer in two function. The first one only callable for the current owner to set the pending owner, and the second one only callable by the pending owner to claim their role as owner.

# SpigotLib#updateOwner can emmit redundant event
[It does not check if the previous owner is equal the the new owner address](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L178-L184), leading to redundant event emission. The function should be changed to:

```solidity
function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
    if(msg.sender != self.owner) { revert CallerAccessDenied(); }
    require(newOwner != address(0));
    require(newOwner != self.owner);
    self.owner = newOwner;
    emit UpdateOwner(newOwner);
    return true;
}
```

# SpigotLib operator change should be done in to steps
To avoid mistakenly transfer the operator role to a wrong address, it would be recommendable to divide the operator role transfer in two function. The first one only callable for the current operator to set the pending operator, and the second one only callable by the pending operator to claim their role as operator.

# SpigotLib#updateOperator can emmit redundant event
[It does not check if the previous operator is equal the the new operator address](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L187-L193), leading to redundant event emission. The function should be changed to:

```solidity
function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
    if(msg.sender != self.operator) { revert CallerAccessDenied(); }
    require(newOperator != address(0));
    require(self.operator != newOperator);
    self.operator = newOperator;
    emit UpdateOperator(newOperator);
    return true;
}
```

# SpigotLib#updateWhitelistedFunction can emmit redundant event
[It does not check if the previous treasury is equal the the new treasury address](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L196-L205), leading to redundant event emission. The function should be changed to:

```solidity
function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {
    if(msg.sender != self.operator && msg.sender != self.treasury) {
        revert CallerAccessDenied();
    }

    require(newTreasury != address(0));
    require(newTreasury != self.treasury);
    self.treasury = newTreasury;
    emit UpdateTreasury(newTreasury);
    return true;
}
```

# SpigotLib#updateWhitelistedFunction can emmit redundant event
[It does not check if the function was previously whitelisted or not](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L209-L211), leading to redundant event emission. The function should be changed to:

```solidity
function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
    if(msg.sender != self.owner) {
        revert CallerAccessDenied();
    }
    if(self.whitelistedFunctions[func] != allowed){
        self.whitelistedFunctions[func] = allowed;
        emit UpdateWhitelistFunction(func, allowed);
        return true;
    }
    return false
}

```

# InterestRateCredit: INTEREST_DENOMINATOR should be immutable
Giving that is set through a calculation, ```INTEREST_DENOMINATOR``` should be immutable


# SpigotedLine: Lack of zero address check could lead to redeployment
The constructor does not check if ```spigot_``` and ```swapTarget_``` are diferent from zero address.

# Oracle#constructor: registry can be set to address zero
Doing this would leave the contract useless, needing to redeploy it.