QA:
Would make code more readable if BPS were used at all times instead of a mix of percentages and BPS.

QA:
Comment miss spellings
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L94-L95

QA:
Always include revert messages, so that called knows what caused the revert.
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L161

Low:
I read that this is a partial solution and will be fixed later but still. Since trading is already implemented, why not use it to exchange tokens directly or just send the collateral directly to the lender? This means the arbiter could take the money and not repay the lender. Doesnt seems too safe of an investment for the lender since he could lose funds. Collateral should be sent to the lenders, not the arbiter.
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L69-L83


Low:
Should be checked that minimumCollateralRatio  isn't over 10000
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L48

Low:
Should check that borrower isn't address(0)
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L50
