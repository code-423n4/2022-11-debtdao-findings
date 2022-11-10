**# missing license**

All Contracts miss the license and at least the unlicensed version should be added - https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#spdx-license-identifier
```bash
// SPDX-License-Identifier: UNLICENSED
```
---

**# Solidity pragma versioning**

Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.9 but some are fixed to 0.8.9.

---

**# solc version**

Solidity pragma versioning can be upgraded to latest version 0.8.17 as this version has some bugs fixed that still exist in 0.8.9 - https://docs.soliditylang.org/en/v0.8.17/bugs.html

Version 0.8.17 compiles and all tests pass
```bash
[⠒] Compiling 65 files with 0.8.17
[⠰] Solc 0.8.17 finished in 5.98s
contracts/tests/LineFactory.t.sol:LineFactoryTest : Test result: ok. 10 passed; 0 failed; finished in 3.19ms

contracts/tests/EscrowedLine.t.sol:EscrowedLineTest : Test result: ok. 5 passed; 0 failed; finished in 3.03ms

contracts/tests/Escrow.t.sol:EscrowTest : Test result: ok. 29 passed; 0 failed; finished in 11.12ms

contracts/tests/LineLib.t.sol:LineLibTest : Test result: ok. 17 passed; 0 failed; finished in 37.75ms

contracts/tests/SecuredLine.t.sol:SecuredLineTest : Test result: ok. 29 passed; 0 failed; finished in 260.47ms

contracts/tests/InterestRateCredit.t.sol:InterestRateCreditTest : Test result: ok. 6 passed; 0 failed; finished in 330.07ms

contracts/tests/SpigotedLine.t.sol:SpigotedLineTest : Test result: ok. 45 passed; 0 failed; finished in 332.27ms

contracts/tests/Spigot.t.sol:SpigotTest : Test result: ok. 51 passed; 0 failed; finished in 356.26ms

contracts/tests/LineOfCredit.t.sol:LineTestSpecial : Test result: ok. 41 passed; 0 failed; finished in 356.01ms

```

---

**# Unnecessary Imports**
Some contracts have imports that are not used.

[contracts/modules/credit/EscrowedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L9)
```bash
9: LineOfCredit is not used
```

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L3)
```bash
3: Denominations is not used
```

[contracts/modules/credit/SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L2)
```bash
2: IERC20 is not used
```

[contracts/modules/credit/SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L3)
```bash
3: Denominations is not used
8: CreditLib is not used
```

[contracts/modules/escrow/Escrow.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L3)
```bash
3:  Denominations is not used
7:  IOracle is not used
8:  ILineOfCredit is not used
9:  CreditLib is not used
10: LineLib is not used
```

[contracts/modules/factories/LineFactory.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L5)
```bash
5: LineLib is not used
```

[contracts/modules/spigot/Spigot.sol](contracts/modules/spigot/Spigot.sol#L4)
```bash
4: LineLib is not used
```

[contracts/utils/CreditLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L7)
```bash
7: LineLib is not used
```

[contracts/utils/CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L2)
```bash
2: ILineOfCredit is not used
3: IOracle is not used
4: CreditLib is not used
```

[contracts/utils/LineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L2)
```bash
2: IInterestRateCredit is not used
3: ILineOfCredit is not used
4: IOracle is not used
```

[contracts/utils/SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L4)
```bash
4: ISpigotedLine is not used
```

[contracts/utils/SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L3)
```bash
3: ReentrancyGuard is not used
```

---

**# use uint256 instead of uint for better consistency**

[contracts/modules/credit/SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L20)
```bash
20: uint ttl_
```

[contracts/utils/CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51)
```bash
51: uint i = 1
```

[contracts/utils/LineFactoryLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L46)
```bash
46: uint ttl
84: uint ttl,
```
---

**# remove unreachable code**

[contracts/utils/SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L207)
```bash
207: return false;
234: return false;
```

**# change mutability to view**

[contracts/modules/oracle/Oracle.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L22)
```bash
22: function getLatestAnswer(address token) external returns (int)
```
can be changed to view as the only function used inside is registry.latestRoundData and this is coming from the 

import "chainlink/interfaces/FeedRegistryInterface.sol";

which has an external view implementation.

---

**# change interface declaration for better consistency**

[contracts/interfaces/ILineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/interfaces/ILineOfCredit.sol#L109)
```bash
109: function addCredit 
should have returns(bytes32 id) like described in comment * @return id
```

this will also resolve a compiler warning in [contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L235)
as you should change it like
```bash
235: returns (bytes32 id)
with
239: id = _createCredit(lender, token, amount);
```

---

**# missleading comment / function implementation**

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L516)
```bash
516: function _sortIntoQ

The return comment "@return - if function executed successfully"
states that the function will throw or return if successful like we have it in some other cases (especially in the interface commnets etc.)
An error should be throwen at the end of the function.
```

---

**# add Error-Information**

For a better understanding when something goes wrong it would help to have a "speaking" error for that cases instead of just a require() without a message.  

[contracts/modules/credit/EscrowedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L64)
```bash
64: require(escrow_.liquidate(amount, targetToken, to));
90: require(escrow.updateLine(newLine));
```

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112)
```bash
112: require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
241: require(interestRate.setRate(id, drate, frate));
260: require(interestRate.setRate(id, drate, frate));
326: require(amount <= credit.principal + credit.interestAccrued);
```

[contracts/modules/credit/SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62)
```bash
62:  require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
143: require(amount <= unusedTokens[credit.token]);
160: require(msg.sender == borrower);
239: require(msg.sender == arbiter);
```

[contracts/utils/EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91)
```bash
91:  require(amount > 0);
105: require(msg.sender == ILineOfCredit(self.line).arbiter());
161: require(amount > 0);
198: require(amount > 0);
216: require(msg.sender == self.line);
```

[contracts/utils/SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L147)
```bash
147: require(ISpigot(spigot).updateOwner(newLine));
```

[contracts/utils/SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96)
```bash
96:  require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
128: require(revenueContract != address(this));
130: require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
155: require(success);
180: require(newOwner != address(0));
189: require(newOperator != address(0));
201: require(newTreasury != address(0));
```

---

**# typos**

[contracts/modules/credit/EscrowedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol)
```shell
--- a/contracts/modules/credit/EscrowedLine.sol
+++ b/contracts/modules/credit/EscrowedLine.sol
@@ -41,9 +41,9 @@ abstract contract EscrowedLine is IEscrowedLine, ILineOfCredit {
   }

   /**
-   * see SecuredlLine.liquidate
+   * see SecuredLine.liquidate
    * @notice sends escrowed tokens to liquidation.
-   * @dev priviliegad function. Do checks before calling.
+   * @dev privileged function. Do checks before calling.
    *
    * @param id - The credit line being repaid via the liquidation
    * @param amount - amount of tokens to take from escrow and liquidate
@@ -72,7 +72,7 @@ abstract contract EscrowedLine is IEscrowedLine, ILineOfCredit {
   /**
    * see SecuredLine.declareInsolvent
    * @notice require all collateral sold off before declaring insolvent
-   *(@dev priviliegad internal function.
+   * @dev privileged internal function.
    * @return isInsolvent - if Escrow contract is currently insolvent or not
   */
   function _canDeclareInsolvent() internal virtual returns(bool) {
@@ -81,9 +81,9 @@ abstract contract EscrowedLine is IEscrowedLine, ILineOfCredit {
   }

   /**
-   * see SecuredlLine.rollover
-   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
-   *(@dev priviliegad internal function.
+   * see SecuredLine.rollover
+   * @notice helper function to allow borrower to easily switch collateral to a new Line after repayment
+   * @dev privileged internal function.
```

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol)
```shell
--- a/contracts/modules/credit/LineOfCredit.sol
+++ b/contracts/modules/credit/LineOfCredit.sol
@@ -35,7 +35,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {

     mapping(bytes32 => Credit) public credits; // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit

-    // Line Financials aggregated accross all existing  Credit
+    // Line Financials aggregated across all existing  Credit
     LineLib.STATUS public status;

     /**
@@ -43,7 +43,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
    * @dev               - A Borrower and a first Lender agree on terms. Then the Borrower deploys the contract using the constructor below.
    *                      Later, both Lender and Borrower must call _mutualConsent() during addCredit() to actually enable funds to be deposited.
    * @param oracle_     - The price oracle to use for getting all token values.
-   * @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.
+   * @param arbiter_    - A neutral party with some special privileges on behalf of Borrower and Lender.
    * @param borrower_   - The debitor for all credit lines in this contract.
    * @param ttl_        - The time to live for all credit lines for the Line of Credit facility (sets the maturity/term of the Line of Credit)
   */
@@ -92,7 +92,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
     }

     /**
-     * @notice - mutualConsent() but hardcodes borrower address and uses the position id to
+     * @notice - mutualConsent() but hardcoded borrower address and uses the position id to
                  get Lender address instead of passing it in directly
      * @param id - position to pull lender address from for mutual consent agreement
     */
@@ -105,7 +105,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {

     /**
      * @notice - evaluates all covenants encoded in _healthcheck from different Line variants
-     * @dev - updates `status` variable in storage if current status is diferent from existing status
+     * @dev - updates `status` variable in storage if current status is different from existing status
      * @return - current health status of Line
     */
     function healthcheck() external returns (LineLib.STATUS) {
@@ -211,7 +211,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
     }

     /**
-      @notice - accrues token demoninated interest on a lender's position.
+      @notice - accrues token denominated interest on a lender's position.
       @dev MUST call any time a position balance or interest rate changes
       @param credit - the lender position that is accruing interest
       @param id - the position id for credit position
@@ -393,7 +393,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
           revert CallerAccessDenied();
         }

-        // ensure all money owed is accounted for. Accrue facility fee since prinicpal was paid off
+        // ensure all money owed is accounted for. Accrue facility fee since principal was paid off
         credit = _accrue(credit, id);
         uint256 facilityFee = credit.interestAccrued;
         if(facilityFee > 0) {
@@ -414,7 +414,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
     //////////////////////

     /**
-      * @notice - updates `status` variable in storage if current status is diferent from existing status.
+      * @notice - updates `status` variable in storage if current status is different from existing status.
       * @dev - privileged internal function. MUST check params and logic flow before calling
       * @dev - does not save new status if it is the same as current status
       * @return status - the current status of the line after updating
@@ -508,7 +508,7 @@ contract LineOfCredit is ILineOfCredit, MutualConsent {
     }

     /**
-     * @notice - Insert `p` into the next availble FIFO position in the repayment queue
+     * @notice - Insert `p` into the next available FIFO position in the repayment queue
                - once earliest slot is found, swap places with `p` and position in slot.
      * @dev - privileged internal function. MUST check params and logic flow before calling
      * @param p - position id that we are trying to find appropriate place for
```

[contracts/modules/credit/SecuredLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol)
```shell
--- a/contracts/modules/credit/SecuredLine.sol
+++ b/contracts/modules/credit/SecuredLine.sol
@@ -45,7 +45,7 @@ contract SecuredLine is SpigotedLine, EscrowedLine, ISecuredLine {
     return s;
   }

-  /// see IsecuredLine.rollover
+  /// see ISecuredLine.rollover
   function rollover(address newLine)
     external
     onlyBorrower
@@ -108,7 +108,7 @@ contract SecuredLine is SpigotedLine, EscrowedLine, ISecuredLine {
   /**
     * @notice Wrapper for SpigotedLine and EscrowedLine internal functions
     * @dev - both underlying calls MUST return true for Line status to change to INSOLVENT
-    * @return isInsolvent - if the entire Line including all collateral sources is fuly insolvent.
+    * @return isInsolvent - if the entire Line including all collateral sources is fully insolvent.
   */
```

[contracts/modules/credit/SpigotedLine.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol)
```shell
--- a/contracts/modules/credit/SpigotedLine.sol
+++ b/contracts/modules/credit/SpigotedLine.sol
@@ -36,7 +36,7 @@ contract SpigotedLine is ISpigotedLine, LineOfCredit, ReentrancyGuard {
     /**
      * @notice - excess unsold revenue claimed from Spigot to be sold later or excess credit tokens bought from revenue but not yet used to repay debt
      *         - needed because the Line of Credit might have the same token being lent/borrower as being bought/sold so need to separate accounting.
-     * @dev    - private variable so other Line modules do not interfer with Spigot functionality
+     * @dev    - private variable so other Line modules do not interfere with Spigot functionality
     */
     mapping(address => uint256) private unusedTokens;

@@ -44,7 +44,7 @@ contract SpigotedLine is ISpigotedLine, LineOfCredit, ReentrancyGuard {
      * @notice - The SpigotedLine is a LineofCredit contract with additional functionality for integrating with a Spigot.
                - allows Borrower or Lender to repay debt using collateralized revenue streams
      * @param oracle_ - price oracle to use for getting all token values
-     * @param arbiter_ - neutral party with some special priviliges on behalf of borrower and lender
+     * @param arbiter_ - neutral party with some special privileges on behalf of borrower and lender
      * @param borrower_ - the debitor for all credit positions in this contract
      * @param spigot_ - Spigot smart contract that is owned by this Line
      * @param swapTarget_ - 0x protocol exchange address to send calldata for trades to exchange revenue tokens for credit tokens
@@ -82,8 +82,8 @@ contract SpigotedLine is ISpigotedLine, LineOfCredit, ReentrancyGuard {

     /**
       * see SecuredLine.declareInsolvent
-      * @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent
-      *(@dev priviliegad internal function.
+      * @notice requires Spigot contract itself to be transferred to Arbiter and sold off to a 3rd party before declaring insolvent
+      * @dev privileged internal function.
       * @return isInsolvent - if Spigot contract is currently insolvent or not
```

[contracts/modules/escrow/Escrow.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol)
```shell
--- a/contracts/modules/escrow/Escrow.sol
+++ b/contracts/modules/escrow/Escrow.sol
@@ -69,7 +69,7 @@ contract Escrow is IEscrow {

     /**
     * @notice - Allows current owner to transfer ownership to another address
-    * @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever
+    * @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transferred `line` is set forever
     * @return didUpdate - if function successfully executed or not
     */
     function updateLine(address _line) external returns(bool) {
@@ -92,8 +92,8 @@ contract Escrow is IEscrow {
     }

     /**
-     * @notice - allows  the lines arbiter to  enable thdeposits of an asset
-     *        - gives  better risk segmentation forlenders
+     * @notice - allows  the lines arbiter to  enable the deposits of an asset
+     *         - gives  better risk segmentation for lenders
      * @dev - whitelisting protects against malicious 4626 tokens and DoS attacks
```

[contracts/modules/interest-rate/InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol)
```shell
--- a/contracts/modules/interest-rate/InterestRateCredit.sol
+++ b/contracts/modules/interest-rate/InterestRateCredit.sol
@@ -15,7 +15,7 @@ contract InterestRateCredit is IInterestRateCredit {
     mapping(bytes32 => Rate) public rates; // position id -> lending rates

     /**
-     * @notice Interest rate / acrrued interest calculation contract for Line of Credit contracts
+     * @notice Interest rate / accrued interest calculation contract for Line of Credit contracts
      */
```

[contracts/modules/spigot/Spigot.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol)
```shell
--- a/contracts/modules/spigot/Spigot.sol
+++ b/contracts/modules/spigot/Spigot.sol
@@ -131,7 +131,7 @@ contract Spigot is ISpigot, ReentrancyGuard {

      * @notice - uses predefined function in revenueContract settings to transfer complete control and ownership from this Spigot to the Operator
      *         - sends any escrowed tokens to the Spigot Owner.
-     * @dev - revenuContract's transfer func MUST only accept one paramteter which is the new owner.
+     * @dev - revenueContract's transfer func MUST only accept one parameter which is the new owner.
      * @dev - callable by `owner`
```

[contracts/utils/CreditLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol)
```shell
--- a/contracts/utils/CreditLib.sol
+++ b/contracts/utils/CreditLib.sol
@@ -121,7 +121,7 @@ library CreditLib {
   /**
     * see ILineOfCredit._createCredit
     * @notice called by LineOfCredit._createCredit during every repayment function
-    * @param oracle - interset rate contract used by line that will calculate interest owed
+    * @param oracle - interest rate contract used by line that will calculate interest owed
    */
   function create(
       bytes32 id,
@@ -198,7 +198,7 @@ library CreditLib {
   /**
     * see ILineOfCredit.withdraw
     * @notice called by LineOfCredit.withdraw during every repayment function
-    * @param credit - The lender position that is being bwithdrawn from
+    * @param credit - The lender position that is being withdrawn from
    */
   function withdraw(
     ILineOfCredit.Credit memory credit,
@@ -235,7 +235,7 @@ library CreditLib {
   /**
     * see ILineOfCredit._accrue
     * @notice called by LineOfCredit._accrue during every repayment function
-    * @param interest - interset rate contract used by line that will calculate interest owed
+    * @param interest - interest rate contract used by line that will calculate interest owed
    */
   function accrue(
     ILineOfCredit.Credit memory credit,
@@ -248,7 +248,7 @@ library CreditLib {
       // interest will almost always be less than deposit
       // low risk of overflow unless extremely high interest rate

-      // get token demoninated interest accrued
+      // get token denominated interest accrued
```

[contracts/utils/LineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol)
```shell
--- a/contracts/utils/LineLib.sol
+++ b/contracts/utils/LineLib.sol
@@ -54,7 +54,7 @@ library LineLib {
     /**
      * @notice - Receive ETH or ERC20 token at this contract from an external contract
      * @param token - address of token to receive. Denominations.ETH for raw ETH
-     * @param sender - address that is sendingtokens/ETH
+     * @param sender - address that is sending tokens/ETH
      * @param amount - amount of tokens to send
```

[contracts/utils/MutualConsent.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol)
```shell
--- a/contracts/utils/MutualConsent.sol
+++ b/contracts/utils/MutualConsent.sol
@@ -25,7 +25,7 @@ abstract contract MutualConsent {
     /* ============ Modifiers ============ */

     /**
-    * @notice - allows a function to be called if only two specific stakeholders signoff on the tx data
+    * @notice - allows a function to be called if only two specific stakeholders sign-off on the tx data
     *         - signers can be anyone. only two signers per contract or dynamic signers per tx.
     */
```

[contracts/utils/SpigotedLineLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol)
```shell
--- a/contracts/utils/SpigotedLineLib.sol
+++ b/contracts/utils/SpigotedLineLib.sol
@@ -42,7 +42,7 @@ library SpigotedLineLib {
                             This feature allows a Borrower to take advantage of an increase in the value of the revenue token compared
                             to the credit token and to in effect use less revenue tokens to be later used to repay the same amount of debt.
      * @dev                 MUST trade all available claimTokens to targetTokens
-     * @dev                 priviliged internal function
+     * @dev                 privileged internal function
      * @param claimToken    - The revenue token escrowed in the Spigot to sell in trade
      * @param targetToken   - The credit token that needs to be bought in order to pat down debt. Always `credits[ids[0]].token`
      * @param swapTarget    - The 0x exchange router address to call for trades
@@ -186,7 +186,7 @@ library SpigotedLineLib {

     /**

-   * @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either
+   * @notice -  Transfers ownership of the entire Spigot and its revenue streams from its then Owner to either
```