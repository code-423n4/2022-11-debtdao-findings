## Redundant `require`
At `claimRevenue` the `require` for `LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount)` is redundant as `sendOutTokenOrETH` will always return true or revert.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L96
```
        if(claimed > escrowedAmount) {
            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
        }
```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L34
```
    function sendOutTokenOrETH(
      address token,
      address receiver,
      uint256 amount
    )
      external
      returns (bool)
    {
        if(token == address(0)) { revert TransferFailed(); }
        
        // both branches revert if call failed
        if(token!= Denominations.ETH) { // ERC20
            IERC20(token).safeTransfer(receiver, amount);
        } else { // ETH
            payable(receiver).transfer(amount);
        }
        return true;
    }

```

