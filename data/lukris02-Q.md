# QA Report for Debt DAO contest
## Overview
During the audit, 9 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | Order of Functions | Non-Critical | 4
NC-2 | Order of Layout | Non-Critical | 2
NC-3 | Spaces between the control structures | Non-Critical | 87
NC-4 | Open TODOs | Non-Critical | 2
NC-5 | Open question | Non-Critical | 3
NC-6 | Typos | Non-Critical | 26
NC-7 | Maximum line length exceeded | Non-Critical | 50+
NC-8 | Missing NatSpec | Non-Critical | 50+
NC-9 | No error message in ```require``` | Non-Critical | 23

## Non-Critical Risk Findings(9)
### NC-1. Order of Functions
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
1) constructor
2) receive function (if exists)
3) fallback function (if exists)
4) external
5) public
6) internal
7) private
##### Instances
receive() function at the end but should be after constructor:
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272

external functions between internal:
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L48-L104

public functions before external:
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L34-L84
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L29-L58

##### Recommendation
Reorder functions where possible.
#
### NC-2. Order of Layout
##### Description
According to [Order of Layout](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout), inside each contract, library or interface, use the following order:
1) Type declarations
2) State variables
3) Events
4) Modifiers
5) Functions
##### Instances
Modifiers after constructor:
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L78
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L25

##### Recommendation
Place modifiers before constructor.
#
### NC-3. No space between the control structures
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#control-structures), there should be a single space between the control structures ```if```, ```while```, and ```for``` and the parenthetic block representing the conditional.
##### Instances
- [```for(uint256 i; i < len; ++i) {```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L23) 
- [```for(uint i = 1; i < len; ++i) {```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51) 
- and 85 instances of ```if(```

##### Recommendation
Change:
```
if(...) {
    ...
}
```
to:
```
if (...) {
    ...
}
```
#
### NC-4. Open TODOs
##### Instances
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140
- https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145

##### Recommendation
Resolve issues.
#
### NC-5. Open question
##### Instances
- [```[Bob - consider renaming to newIds```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L17)
- [```* Bob - consider renaming this function removeId()```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L18) 
- [```self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L117) 

#
### NC-6. Typos
##### Instances
- [```// Line Financials aggregated accross all existing  Credit```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L37) => ```across```
- [```* @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L45) => ```privileges```
- [```* @dev - updates `status` variable in storage if current status is diferent from existing status```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L107) => ```different```
- [```@notice - accrues token demoninated interest on a lender's position.```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L213) => ```denominated```
- [```// ensure all money owed is accounted for. Accrue facility fee since prinicpal was paid off```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L395) => ```principal```
- [```* @notice - updates `status` variable in storage if current status is diferent from existing status.```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L416) => ```different```
- [```* @notice - Insert `p` into the next availble FIFO position in the repayment queue```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L510) => ```available```
- [```* @param arbiter_ - neutral party with some special priviliges on behalf of borrower and lender```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L46) => ```privileges```
- [```* @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84) => ```itself```
- [```* @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84) => ```transferred```
- [```* @dev     - priviliged internal function```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L180) => ```privileged```
- [```* @return isInsolvent - if the entire Line including all collateral sources is fuly insolvent.```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L110) => ```fully```
- [```* @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84) => ```switch```
- [```* @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84) => ```repayment```
- [```* @dev - revenuContract's transfer func MUST only accept one paramteter which is the new owner.```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L133) => ```parameter```
- [```* @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71) => ```transferred```
- [```* @notice - allows  the lines arbiter to  enable thdeposits of an asset```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L94) => ```the deposits```
- [```*        - gives  better risk segmentation forlenders```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L95) => ```for lenders```
- [```* @notice Interest rate / acrrued interest calculation contract for Line of Credit contracts```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L17) => ```accrued```
- [```* @param credit - The lender position that is being bwithdrawn from```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L200) => ```withdrawn```
- [```// emit events before seeting to 0```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221) => ```setting```
- [```* @param interest - interset rate contract used by line that will calculate interest owed```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L237) => ```interest```
- [```// get token demoninated interest accrued```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L250) => ```denominated```
- [```* @dev                 priviliged internal function```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L44) => ```privileged```
- [```* @param spigot        - The Spigot to claim from. Must be owned by adddress(this)```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L48) => ```address```
- [```* @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L188) => ```revenue```

#
### NC-7. Maximum line length exceeded
##### Description
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#maximum-line-length), maximum suggested line length is 120 characters.
##### Instances
More than 50 instances.

##### Recommendation
Make the lines shorter.
#
### NC-8. Missing NatSpec
##### Description
NatSpec is missing for more than 50 functions.

##### Recommendation
Add NatSpec for all functions.
#
### NC-9. No error message in ```require```
##### Instances
- [```require(uint(status) >= uint( LineLib.STATUS.ACTIVE));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112) 
- [```require(interestRate.setRate(id, drate, frate));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L241) 
- [```require(interestRate.setRate(id, drate, frate));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L259) 
- [```require(amount <= credit.principal + credit.interestAccrued);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326) 
- [```require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62) 
- [```require(amount <= unusedTokens[credit.token]);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143) 
- [```require(msg.sender == borrower);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160) 
- [```require(msg.sender == arbiter);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239) 
- [```require(escrow_.liquidate(amount, targetToken, to));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L64) 
- [```require(escrow.updateLine(newLine));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L90) 
- [```require(amount > 0);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91) 
- [```require(msg.sender == ILineOfCredit(self.line).arbiter());```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105) 
- [```require(amount > 0);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161) 
- [```require(amount > 0);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198) 
- [```require(msg.sender == self.line);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216) 
- [```require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96) 
- [```require(revenueContract != address(this));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L128) 
- [```require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L130) 
- [```require(success);```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L155) 
- [```require(newOwner != address(0));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180) 
- [```require(newOperator != address(0));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189) 
- [```require(newTreasury != address(0));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201) 
- [```require(ISpigot(spigot).updateOwner(newLine));```](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L147) 

##### Recommendation
Add error messages.