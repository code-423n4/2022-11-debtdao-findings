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
