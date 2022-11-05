Better to not call `credit.deposit` and `credit.interestRepaid` from memory twice in the function `_close`. Instead, store these values in a local variable, and read from there.
```
function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
        if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }

        // return the Lender's funds that are being repaid
        if (credit.deposit + credit.interestRepaid > 0) {
            LineLib.sendOutTokenOrETH(
                credit.token,
                credit.lender,
                credit.deposit + credit.interestRepaid
            );
        }

        delete credits[id]; // gas refunds

        // remove from active list
        ids.removePosition(id);
        unchecked { --count; }

        // If all credit lines are closed the the overall Line of Credit facility is declared 'repaid'.
        if (count == 0) { _updateStatus(LineLib.STATUS.REPAID); }

        emit CloseCreditPosition(id);

        return true;
    }
```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L483