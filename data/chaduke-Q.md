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
Since msg.data is has a variable length, use ``abi.encode()`` instead of ``abi.encodePacked()``
.

QA8: the whole EscrowedLine.sol can be eliminated by declaring the ONLY ``escrow`` state variable directly in the ``SecureLine* contract because
1) Every function is internal and a one-line-wrapper of a function in escrow. Therefore, it is better to call escrow.fun directly without introducing the wrapper. 
2) One exception is the ``_canDeclareInsolvent()`` which implements a simple predicate *if escrow.getCollateralValues() !=0)*. However, this can be either expressed directly where it is needed or be a function of escrow as well. 
3) This will greatly reduce the unnecessary code and complexity and attack surface.


QA9: when update the status, the event should include both the old status and the new status
```
emit UpdateStatus(oldstatus, newstaus)
```
QA10. As a matter of fact, the FIVE versions of _init/init() (Escrowline.sol, Spigotedline.sol, LineOfCredit.sol and SecuredLine.sol) can easily consolidated into ONE _init function in ``SecureLine.sol` as they are just wrappers of two conditions to make sure escrow and spigot are assigned to the current contract address of SecuredLinesol. This greatly simplifies the code and unnecessary complexity and thus the attack surface.

```
function _init() internal override(SpigotedLine, EscrowedLine) virtual returns(LineLib.STATUS) {
    if(spigot.owner() != address(this)) return LineLib.STATUS.UNINITIALIZED;  // @audit: check condition 1
    if(escrow.line() != address(this)) return LineLib.STATUS.UNINITIALIZED;  // @audit: check condition 2
    return LineLib.STATUS.ACTIVE;
  }
```

QA10: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L38
emit MutualConsentRegistered(newHash) is performed only for the first party when he/she consented, should be emitted for both parties. In addition, add another argument for the event - the address of the party who consented. 


QA11: current implementation of ``_mutualConsent()`` only works for one time due to line 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L57
As a result, all those functions that use _mutualConsent() as the modifier, such as ``addCredit()``, ``setRates()``, ``increaseCredit()`` can be performed once and then a new consent needs to be in place. Since they all use the same underlying ``_mutualConsent``, there is also the limitation that two parties agree to ``addCredit()`` but one party can abuse it to ``increaseCredit()``. That is, what has been mutually agreeed on is not explicit and thus can be abused. 

QA12: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L132
block.timestamp should be strictly greater than deadline for a line to be considered liquidable. 

