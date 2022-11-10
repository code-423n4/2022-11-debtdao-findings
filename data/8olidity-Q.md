## [low]There is no limit on the address of `arbiter`

1. `arbiter` set to` address(0)`, then no one can do the clearing.

2. If the `arbiter` is set as `borrower`, then `borrower` can go into liquidation

```solidity
    function declareInsolvent() external whileBorrowing returns(bool) {
        if(arbiter != msg.sender) { revert CallerAccessDenied(); }
        if(LineLib.STATUS.LIQUIDATABLE != _updateStatus(_healthcheck())) {
            revert NotLiquidatable();
        }

        if(_canDeclareInsolvent()) {
            _updateStatus(LineLib.STATUS.INSOLVENT);
            return true;
        } else {
          return false;
        }
    }
```


## [info] `_canDeclareInsolvent()` is meaningless because it returned true
```solidity
	// contracts/modules/credit/LineOfCredit.sol
	function declareInsolvent() external whileBorrowing returns(bool) {
        if(arbiter != msg.sender) { revert CallerAccessDenied(); }
        if(LineLib.STATUS.LIQUIDATABLE != _updateStatus(_healthcheck())) {
            revert NotLiquidatable();
        }

        if(_canDeclareInsolvent()) { //@audit  
            _updateStatus(LineLib.STATUS.INSOLVENT);
            return true;
        } else {
          return false;
        }
    }
```


