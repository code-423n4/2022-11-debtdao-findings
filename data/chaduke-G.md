G1. add modifier onlyOwner to   function *claimEscrow* in Spigot.sol can save gas since the owner check can be performed first without calling state.claimEscrow(token). (Line 90)
```
function claimEscrow(address token)
        external
        onlyOwner
        nonReentrant
        returns (uint256 claimed) 
    {
        return state.claimEscrow(token);
    }

    modifier onlyOwner() {
        if(msg.sender != state.owner) revert CallerAccessDenied();
        _;
    }
```
Now we can delete line: 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L109

G2. add modifier onlyOperator to   function *operate* in Spigot.sol can save gas since the operator check can be performed first without calling state.operate(). (Line 109)
```
function operate(address revenueContract, bytes calldata data) external         
onlyOperator 
returns (bool)
        {
        return state.operate(revenueContract, data);
    }

modifier onlyOperator() {
        if(msg.sender != state.operator) revert CallerAccessDenied();
        _;
}
```
Now we can delete line: 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L62

 
 