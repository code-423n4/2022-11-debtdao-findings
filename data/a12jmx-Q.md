1.

Missing license on line 01 in all contracts except for contract Oracle.sol

Consider adding the same license on all contracts if it's the same license

or consider as is best practice if no license required for whatever reason to rather add-in

// SPDX-License-Identifier: UNLICENSED

2. 

Some contracts use pragma solidity ^0.8.9 while other contracts only use 0.8.9

Contracts that use ^0.8.9 :

	LineOfCredit.sol
	SpigotedLine.sol
	SecuredLine.sol
	Oracle.sol
	InterestRateCredit.sol
	
Contracts that use 0.8.9 :

	EscrowedLine.sol
	Spigot.sol
	Escrow.sol
	LineFactory.sol
	CreditLib.sol
	LineLib.sol
	CreditListLib.sol
	EscrowLib.sol
	SpigotLib.sol
	SpigotedLineLib.sol
	MutualConsent.sol
	LineFactoryLib.sol
	
As there will be no fundamental change to code operations or interoperability and
"future-proof" contracts, consider changing contracts that are just 0.8.9 also to ^0.8.9

3.

There is a missing open line as best practice after the pragma line in some contracts

Contracts without open line after pragma:

	SecuredLine.sol
	CreditLib.sol
	LineLib.sol
	CreditListLib.sol
	
Consider adding a line after the pragma in the contracts above as it will improve readability and 
code consistency

NOTE: Following steps 1 - 3 will also provide code consistency throughout all contracts in scope

4.

Grammer issues in comments in:

4.1

Contract: LineOfCredit.sol

4.1.1

	line 37

	"across" is misspelled as "accross"

Modified Comment:

	// Line Financials aggregated across all existing  Credit

4.1.2

	line 45

	"privileges" is misspelled as "priviliges"

Modified Comment:

	* @param arbiter_    - A neutral party with some special privileges on behalf of Borrower and Lender.

4.1.3

	line 46

	"debtor" is misspelled as "debitor"

Modified Comment:

	* @param borrower_   - The debtor for all credit lines in this contract.

4.1.4

	line 95

	"indirectly" is misspelled as "in directly"

Modified Comment:

	get Lender address instead of passing it indirectly

4.1.5

	lines 107, and 416

	"different" is misspelled as "diferent"

Modified Comments:

	* @dev - updates `status` variable in storage if current status is different from existing status

	* @notice - updates `status` variable in storage if current status is different from existing status.

4.1.6

	line 213

	"denominated" is misspelled as "demoninated"

Modified Comment:

	@notice - accrues token denominated interest on a lender's position.

4.1.7

	line 395

	"principal" is misspelled as "prinicpal"

Modified Comment:

	// ensure all money owed is accounted for. Accrue facility fee since principal was paid off

4.1.8

	line 444

	"double add" should be hyphenated as "double-add"

Modified Comment:

	// MUST not double-add the credit line. otherwise we can not _close()

4.1.9

	line 501

	there is an extra "the" before "overall"

Modified Comment:

	// If all credit lines are closed the overall Line of Credit facility is declared 'repaid'.

4.1.10

	line 510

	"available" is misspelled as "availble"

Modified Comment:

	* @notice - Insert `p` into the next available FIFO position in the repayment queue

4.2.
	
Contract: SpigotedLine.sol

4.2.1

	line 38

	"interfere" is misspelled as "interfer"

Modified Comment:

	* @dev    - private variable so other Line modules do not interfere with Spigot functionality

4.2.2

	line 46

	"privileges" is misspelled as "priviliges"

Modified Comment:

	* @param arbiter_ - neutral party with some special privileges on behalf of borrower and lender

4.2.3

	line 47

	"debtor" is misspelled as "debitor"

Modified Comment:

	* @param borrower_ - the debtor for all credit positions in this contract

4.2.4

	line 84

	"itself" is misspelled as "itselgf"

	"transferred" is misspelled as "transfered"

Modified Comment:
	
	* @notice requires Spigot contract itself to be transferred to Arbiter and sold off to a 3rd party before declaring insolvent

4.2.5

	line 164

	"don't" is misspelled as "dont"

Modified Comment:

	spigot.claimEscrow(claimToken) : // same asset. don't trade

4.2.6

	line 178

	"token" should be "tokens"

Modified Comment:

	*          - MUST trade all available claim tokens to target credit tokens.

4.2.7

	line 179
	
	"debt" is misspelled as "dent"

Modified Comment:

	*          - Excess credit tokens not used to repay debt are stored in `unused`

4.2.8

	line 180

	"privileged" is misspelled as "priviliged"

Modified Comment:

	* @dev     - privileged internal function

4.3

Contract: SecuredLine.sol

4.3.1

	line 58

	"don't" is misspelled as "dont"

Modified Comment:

	// we don't check borrower is same on both lines because borrower might want new address managing new line

4.3.2

	line 72

	the "the" before "deem" should be "they"

Modified Comment:

	*          - current implementation just sends "liquidated" tokens to Arbiter to sell off how they deem fit and then manually repay with DepositAndRepay

4.3.3

	line 77

	the "of" after "sold" should be "off"

Modified Comment:

	* @param targetToken - token in escrow that will be sold off to repay position

4.3.4

	line 110

	"fully" is misspelled as "fuly"

Modified Comment:

	* @return isInsolvent - if the entire Line including all collateral sources is fully insolvent.

4.4

Contract: EscrowedLine.sol

4.4.1

	line 45

	"privileged" is misspelled as "priviliegad"

Modified Comment:

	* @dev privileged function. Do checks before calling.

4.4.2

	line 84

	"switch" is misspelled as "swithc"

	"repayment" is misspelled as "repyment"

Modified Comment:

	* @notice helper function to allow borrower to easily switch collateral to a new Line after repayment

4.5

Contract: Spigot.sol

4.5.1

	lines 29, and 212

	"revenue generating" should be hyphenated as "revenue-generating"

Modified Comments:

	on revenue-generating contracts controlled by the Spigot.

	on the revenue-generating smart contracts whilst the Spigot is attached.
	
4.5.2	
	
	line 133

	"parameter" is misspelled as "paramteter"

Modified Comment:

	* @dev - revenuContract's transfer func MUST only accept one parameter which is the new owner.

4.6

Contract: Escrow.sol

4.6.1

	line 71

	"setup" should be 2 words "set up"

	"transferred" is misspelled as "transfered"

Modified Comment:

	* @dev    - Used if we set up Escrow before Line exists. Line has no way to interface with this function so once transferred `line` is set forever

4.6.2

	line 94

	there is a typo with "thdeposits" which should be "the deposits"

Modified Comment:

	* @notice - allows  the lines arbiter to  enable the deposits of an asset

4.6.3

	line 141

	"anytime" should be 2 words as "any time"

	"off" should be "on"

Modified Comment:

	*         line can liquidate at any time based on other covenants besides cratio

4.7
	
Contract: Oracle.sol

4.7.1

	line 11

	"request" should be "requests"

Modified Comment:

	*          - only makes requests for USD prices and returns results in standard 8 decimals for Chainlink USD feeds

4.8

Contract: InterestRateCredit.sol

4.8.1

	line 17

	"accrued" is misspelled as "acrrued"

Modified Comment:

	* @notice Interest rate / accrued interest calculation contract for Line of Credit contracts

4.9

Contract: LineFactory.sol

4.9.1

	line 132

	the "are" should be "is"

Modified Comment:

	*   @dev   The `cratio` in the CoreParams is not used, due to the fact

4.9.2

	line 171

	"of" should be "on"

	consider adding "the" before "config" for better readability

Modified Comment:

	@notice sets up new line based on the config of old line. Old line does not need to have REPAID status for this call to succeed.

4.10

Contract: CreditLib.sol

4.10.1

	lines 123, and 237

	"interest" is misspelled as "interset"

Modified Comments:
   
	* @param oracle - interest rate contract used by line that will calculate interest owed

	* @param interest - interest rate contract used by line that will calculate interest owed

4.10.2

	line 200

	there is a typo with "withdrawn" where the "b" before the "w" of "withdrawn" should be removed

Modified Comment:

	* @param credit - The lender position that is being withdrawn from

4.10.3

	line 221

	"setting" is misspelled as "seeting"

Modified Comment:

	// emit events before setting to 0

4.10.4

	line 248

	"high interest" should be hyphenated as "high-interest"

Modified Comment:

	// low risk of overflow unless extremely high-interest rate

4.10.5

	line 250

	"denominated" is misspelled as "demoninated"

Modified Comment:

	// get token denominated interest accrued

4.11

Contract: EscrowLib.sol

4.11.1

	line 15

	"token" should be "tokens"

Modified Comment:

	/// if lenders allow tokens as collateral. ensures uniqueness in collateralTokens

4.11.2

	line 121

	"save" should be "saves"

Modified Comment:

	// if 4626 saves the underlying token to use for oracle pricing

4.12

Contract: SpigotLib.sol

4.12.1

	line 53

	"doesnt" should be "doesn't"

Modified Comment:

	// cap so uint doesn't overflow in split calculations.
     
4.12.2     
      
	line 69

	"cant" should be "can't"
  
Modified Comment:  
  
	// can't claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case

4.13

Contract: SpigotedLineLib.sol

4.13.1

	line 42

	"less" should be "fewer"

Modified Comment:

	to the credit token and to in effect use fewer revenue tokens to be later used to repay the same amount of debt.

4.13.2

	line 44

	"privileged" is misspelled as "priviliged"

Modified Comment:

	* @dev                 privileged internal function

4.13.3

	line 48

	"address" is misspelled as "adddress"

Modified Comment:

	* @param spigot        - The Spigot to claim from. Must be owned by address(this)

4.13.4

	line 188

	"revenue" is misspelled as "revenuw"

Modified Comment:

	* @notice -  Transfers ownership of the entire Spigot and its revenue streams from its then Owner to either 

4.13.5

	line 189

	there is an unnecessary "been" before "fully"

Modified Comment:

	the Borrower (if a Line of Credit has been fully repaid) or 

4.14

Contract: MutualConsent.sol

4.14.1

	line 28

	"signoff" should be two words as "sign off"

Modified Comment:

	* @notice - allows a function to be called if only two specific stakeholders sign off on the tx data

4.15

Contract: LineFactoryLib.sol

4.15.1

	line 34

	"of" should be "on"

	consider adding "the" before "config" for better readability

Modified Comment:

	@notice sets up new line based on the config of old line. Old line does not need to have REPAID status for this call to succeed.

5.

There are unnecessary spaces in the comments of:

5.1

Contract: SpigotedLine.sol

	line 31

	there's an unnecessary space between "Credit" and "is"

Modified Comment:

	/// @notice % of revenue tokens to take from Spigot if the Line of Credit is healthy. 0 decimals
	
Contract: Escrow.sol

	line 94

	* @notice - allows  the lines arbiter to enable thdeposits of an asset

NOTE: If step 4 is followed, there is a typo in this comment and the comment will become

	* @notice - allows  the lines arbiter to enable the deposits of an asset