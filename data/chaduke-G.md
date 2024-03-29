G1. add modifier ``onlyOwner`` to   function ``claimEscrow`` in Spigot.sol can save gas since the owner check can be performed first without calling state.claimEscrow(token). (Line 90)
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

 G3: unfold the state struct and let each item inside to become a state variable. This will not only improve the readibility of the code but also save much gas because we can save the gas to calculate the address of each member item inside.
 

G4: add modifier ``onlyLine`` to function ``updateLine`` in ``escrow.sol`` and inline the implementation of the ``updateLine`` function in the library ``escrowlib.sol`` since it is only called once
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L74
```
function updateLine(address _line) external 
onlyLine
returns(bool) {
      function updateLine(EscrowState storage self, address _line) external returns(bool) {
      self.line = _line;
      return true;
 }
    }

modifier onlyLine() {
        if(msg.sender != state.line) revert CallerAccessDenied();
        _;
}
```

G5: add modifier ``onlyArbiter`` to function ``enableCollateral`` in ``escrow.sol``.
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L00
```
function enableCollateral(address token) external 
onlyArbiter
returns (bool) {
        return state.enableCollateral(oracle, token);
    }
}

modifier onlyArbiter() {
        if(msg.sender != ILineOfCredit(state.line).arbiter()) revert CallerAccessDenied();
        _;
}
```

G6: use the short-circuit rule to eliminate one if-statement for ``enableCollaberal`` function in ``escrowlib.sol``:
```
    function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {
       if(self.enabled[token] return true;

        IEscrow.Deposit memory deposit = self.deposited[token]; // gas savings
        if (token == Denominations.ETH) {
                // enable native eth support
                deposit.asset = Denominations.ETH;
                deposit.assetDecimals = 18;
            } else {
                (bool passed, bytes memory tokenAddrBytes) = token.call(
                    abi.encodeWithSignature("asset()")
                );

                bool is4626 = tokenAddrBytes.length > 0 && passed;
                deposit.isERC4626 = is4626;
                // if 4626 save the underlying token to use for oracle pricing
                deposit.asset = !is4626
                    ? token
                    : abi.decode(tokenAddrBytes, (address));

                int256 price = IOracle(oracle).getLatestAnswer(deposit.asset);
                if (price <= 0) {
                    revert InvalidCollateral();
                }

                (bool successDecimals, bytes memory decimalBytes) = deposit
                    .asset
                    .call(abi.encodeWithSignature("decimals()"));
                if (decimalBytes.length > 0 && successDecimals) {
                    deposit.assetDecimals = abi.decode(decimalBytes, (uint8));
                } else {
                    deposit.assetDecimals = 18;
                }
            }

            // update collateral settings
            self.enabled[token] = true;
            self.deposited[token] = deposit;
            self.collateralTokens.push(token);
            emit EnableCollateral(deposit.asset);
         return true;
    }

```

G7. The following two wrapper funcdtions can be eliminated to save gas. One can call the ``_getLatestCollateralRatio(self, oracle)`` and ``_getCollateralValue`` directly. 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L182-L189

G8: delete the function ``_healthcheck()`` in Escrowedline.sol to save gas since one can simply call escrow.isLiquitable() directly. This also eliminate one version of _healthcheck() in the code as too many versions of _healthcheck/healthcheck is not necessary (only two logics are needed actually). 

G9: delete function ``_liquidate`` in Escrowedline.sol to save gas since one can simply call ``escrow.liquidate(amount, targetToken, to)`` directly. This will also improve the readability of the code.


G10. The assignment line can be eliminated to save gas:
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L38
```
function _init() internal override(SpigotedLine, EscrowedLine) virtual returns(LineLib.STATUS) {
    if(SpigotedLine._init() != s || EscrowedLine._init() != s) 
      return LineLib.STATUS.UNINITIALIZED;
    
    
    return LineLib.STATUS.ACTIVE;
  }
```

G11.  https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L73
No need to call ``LineOfCredit._init();``, which just returns a constant, the function can be simplified by:
```
 function _init() internal virtual override(LineOfCredit) returns(LineLib.STATUS) {
      if(spigot.owner() != address(this)) return LineLib.STATUS.UNINITIALIZED;
      return Linelib.STATUS.ACTIVE;
    }
```

G12, function ``_accrue()`` in ``LineOfCredit.sol`` can be eliminated since it is a one-line wrapper of function ``CreditLib.accure()``, one can call the later directly. Line 206 can be replaced by
```
          credits[id] = CreditLib.accrue(credit, id, address(interestRate));
```

G13: https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L388
The ``close()* function should performance a zero-principal check first to save gas. Currently, it is checked inside the ``_close()'' function, which is too late. The new version of ``close()`` function is as follows. 
```
function close(bytes32 id) external payable override returns (bool) {
        Credit memory credit = credits[id];
        if(credit.principal !=0 ) revert CloseFailedWithPrincipal();
 
        address b = borrower; // gas savings
        if(msg.sender != credit.lender && msg.sender != b) {
          revert CallerAccessDenied();
        }

        // ensure all money owed is accounted for. Accrue facility fee since prinicpal was paid off
        credit = _accrue(credit, id);
        uint256 facilityFee = credit.interestAccrued;
        if(facilityFee > 0) {
          // only allow repaying interest since they are skipping repayment queue.
          // If principal still owed, _close() MUST fail
          LineLib.receiveTokenOrETH(credit.token, b, facilityFee);

          credit = _repay(credit, id, facilityFee);
        }

        _close(credit, id); // deleted; no need to save to storage

        return true;
    }
```