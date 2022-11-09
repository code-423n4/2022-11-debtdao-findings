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

## Condition is never True
In `claimAndTrade` function in `SpigotedLineLib.sol`, the following condition `if(claimToken == targetToken)`
 will never be true in current implementation. As previous call paths check for the same condition.

```
function claimAndTrade(
        address claimToken,
        address targetToken,
        address payable swapTarget,
        address spigot,
        uint256 unused,
        bytes calldata zeroExTradeData
    )
        external 
        returns(uint256, uint256)
    {
        // can't trade into same token. causes double count for unused tokens
        if(claimToken == targetToken) { revert BadTradingPair(); }
```
Previous condition:
```
 function claimAndTrade(address claimToken, bytes calldata zeroExTradeData)
        external
        whileBorrowing
        nonReentrant
        returns (uint256)
    {
        require(msg.sender == borrower);

        address targetToken = credits[ids[0]].token;
        uint256 newTokens = claimToken == targetToken ?
          spigot.claimEscrow(claimToken) : // same asset. dont trade
          _claimAndTrade(                   // trade revenue token for debt obligation
              claimToken,
              targetToken,
              zeroExTradeData
          );

        // add bought tokens to unused balance
        unusedTokens[targetToken] += newTokens;
        return newTokens;
    }
```

## Return will always be `amount` and never 0
In the `sweep` function in `SpigotedLine.sol`. The return statement `return success ? amount : 0;` will always return the amount because `SpigotedLineLib.sweep` always returns true or reverts. Never false. 

SpigotedLine.sol#sweep:
```
function sweep(address to, address token) external nonReentrant returns (uint256) {
        uint256 amount = unusedTokens[token];
        delete unusedTokens[token];

        bool success = SpigotedLineLib.sweep(
          to,
          token,
          amount,
          _updateStatus(_healthcheck()),
          borrower,
          arbiter
        );

        return success ? amount : 0;
    }
```
`SpigotedLineLib.sweep` the `return false` will never be called:
```
    function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {
        if(amount == 0) { revert UsedExcessTokens(token, 0); }

        if (status == LineLib.STATUS.REPAID && msg.sender == borrower) {
            return LineLib.sendOutTokenOrETH(token, to, amount);

        }

        if (
          (status == LineLib.STATUS.LIQUIDATABLE || status == LineLib.STATUS.INSOLVENT) &&
          msg.sender == arbiter
        ) {
            return LineLib.sendOutTokenOrETH(token, to, amount);
        }

        revert CallerAccessDenied();

        return false;
    }
```
`LineLib.sendOutTokenOrETH` always returns True or reverts:
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
