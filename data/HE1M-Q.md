### No. 1
Better to check that if an ERC20 token is going to be transferred to the protocol, no ETH should be sent:
```
function receiveTokenOrETH(
      address token,
      address sender,
      uint256 amount
    )
      external
      returns (bool)
    {
        if(token == address(0)) { revert TransferFailed(); }
        if(token != Denominations.ETH) { // ERC20
            // this line should be added:
            require(msg.value == 0);
            IERC20(token).safeTransferFrom(sender, address(this), amount);
        } else { // ETH
            if(msg.value < amount) { revert TransferFailed(); }
        }
        return true;
    }
```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L59

### No. 2
Any smart contract that uses `transfer()` or `send()` is taking a hard dependency on gas costs by forwarding a fixed amount of gas: 2300. If gas costs are subject to change, then smart contracts can’t depend on any particular gas costs.
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
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48

### No. 3
It is possible to apply grieving attack. When a borrower is going to call `rollover(address newLine)`, the attacker calls the function `init()` in the address of new line, so the status of the new line will change to `LineLib.STATUS.ACTIVE`.
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L48

Now, the borrower transaction for rolling over will be reverted, because the status of new line is not `LineLib.STATUS.UNINITIALIZED`. 
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L57

### No. 4
Line 234 will never be called, it can be removed:
https://github.com/debtdao/Line-of-Credit/blob/c1037d7f873cfb2af48fdaf00e44cd55c7737fc1/contracts/utils/SpigotedLineLib.sol#L234

### No. 5
Maybe better to use `staticcall` to get `decimals`:
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L142