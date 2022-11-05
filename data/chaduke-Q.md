QA1: It is advised to use *send* to send eth for the following line: 
 https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48
Justification: the gas cost for *transfer* might change and might introduce reentrancy attack; see
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

QA2: implementation and documentation not consistent, 4 decimals or 5 decimals? 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42
Make it consistent.

QA3: lock and use the most recent version of solidity 0.8.17 consistently in all files

QA4: 
add modifier ``onlyBorrower`` to function ``releaseCollateral`` in ``escrow.sol`` and eliminate the borrower check in ``releaseCollateral`` in ``escrowlib.sol``. 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L113
```
 function releaseCollateral(
        uint256 amount,
        address token,
        address to
    ) external 
   onlyBorrower
 returns (uint256) {
        return state.releaseCollateral(borrower, oracle, minimumCollateralRatio, amount, token, to);
    }


modifier onlyBorrower() {
        if(msg.sender != borrower) revert CallerAccessDenied();
        _;
}
```

QA5: 
add modifier ``onlyLine` to function ``liquidate`` in ``escrow.sol`` and eliminate the Line check in ``liquidte`` in ``escrowlib.sol``. 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L149
```

 function liquidate(
        uint256 amount,
        address token,
        address to
    ) external
    onlyLine 
    returns (bool) {
        return state.liquidate(amount, token, to);
    }
modifier onlyLine() {
        if(msg.sender != state.line) revert CallerAccessDenied();
        _;
}
```

QA6: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L49
Zero address check is advised for ``oracle``, `arbiter`` and ``borrower`` and range check is suggested for ``ttl``.

QA7: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L38-L60
Since msg.data is has a variable length, use ``abi.encode()`` instead of ``abi.encodePacked()``.

QA8: In LineOfCredit.sol, function ``_healthcheck`` implementation can include the change of status inside so that we do not have to call _updateStaus(_healthcheck()) explicitly. There is no need to check other status inside ``_healthcheck()`` - the only thing we need to check is whether the line is LIQUITABLE or not. We suggest to combine *healthcheck()* and *_healthcheck* into one function. This implementation is much simpler than the original one, see below.

```
function healthcheck() external returns (LineLib.STATUS) {
        // @audit: no need to check other status logic

        // Liquidate if all credit lines aren't closed by deadline
        if (block.timestamp >= deadline && count > 0) {
            emit Default(ids[0]); // can query all defaulted positions offchain once event picked up
            _updateStatus(LineLib.STATUS.LIQUIDATABLE); @audit; update status here
        }
        return status; 
     }
```


