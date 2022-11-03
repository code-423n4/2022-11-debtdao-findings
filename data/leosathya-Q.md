# Report


## QA Report


| |Issue|Instances|
|-|:-|:-:|
| [LOW-1] | Lack Of Zero Address Check | 4 |
| [LOW-2] | LineLib.sol contract sending ERC20 Token and ETH to receiver address, Token or ETH may lost | 1 |
| [N-1] | Error Message Missing In Require Statement | 4 |



### [LOW-01] Lack Of Zero Address Check
Constructor setting address to Immutable state variable, and during this process zero address check missing for the new address that to be set on immutable state variable
 
*Instances (4)*:
```solidity
File: contracts/modules/credit/EscrowedLine.sol

17:         escrow = IEscrow(_escrow);

```

```solidity
File: contracts/modules/escrow/Escrow.sol

48:         minimumCollateralRatio = _minimumCollateralRatio;
49:         oracle = _oracle;
50:         borrower = _borrower;

```

### [LOW-02] LineLib.sol contract sending ERC20 Token and ETH to receiver address, Token or ETH may lost
As there are no zero address checks for receiver address, so if receiver is a zero address then token or eth will lost for ever.
 
*Instances (1)*:
```solidity
File: contracts/utils/LineLib.sol

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

### [N-01] Error Message Missing In Require Statement
There should be a error statement that explain or give hints about cause of function revert
 
*Instances (4)*:
```solidity
File: contracts/modules/credit/EscrowedLine.sol

64:         require(escrow_.liquidate(amount, targetToken, to));
90:         require(escrow.updateLine(newLine));
```

```solidity
File: contracts/modules/credit/LineOfCredit.sol

112:         require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
259:         require(interestRate.setRate(id, drate, frate))

```




