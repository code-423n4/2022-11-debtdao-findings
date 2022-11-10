### Solidity conventional styling/layout not followed
The following Files does not follow standard solidity style conventions with regards to the placement of the events and errors, consider following the solidity standard styleing conventions by placing such things as events, custom errors, constants functions in the correct order when writing contracts such as :-

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L221
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L270

1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions 

https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout

### Open TODO's

Remove open todos which state things like unused code or potential bad side effects of using certain functions or doing certain things etc before deployment live, as this can also point malicious actors into areas of the code that maybe vulnerable.

Remove open todos which state things like unused code or potential bad side effects of using certain functions or doing certain things etc before deployment live, as this can also point malicious actors into areas of the code that maybe vulnerable.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L8
// used for importing NATSPEC docs, not used

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L63
     @dev    - Assumes the only side effect of calling claimFunc on revenueContract is we receive new tokens.
               - Any other side effects could be dangerous to the Spigot or upstream contracts.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L140
        // TODO: test
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L145
        // TODO: test

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L17
remove the Bob comments
  @return newPositions - all active credit lines on the Line of Credit facility after the `id` has been removed [Bob - consider renaming to newIds
     Bob - consider renaming this function removeId()

### Typos found within the project contracts


https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L54
   // require all debt successfully paid already
should be
   // require that all debt is successfully paid already

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L72
             The current implementation just sends "liquidated" tokens to Arbiter to sell off how the deem fit and then manually repay with DepositAndRepay
should be
            The current implementation just sends "liquidated" tokens to the Arbiter to sell off how they deem fit and then manually repay with DepositAndRepay

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L84     * @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent
should be 
	@notice requires Spigot contract itself to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L85
      (@dev priviliegad internal function.
should be 
      (@dev privileged internal function

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L58
    // we dont check borrower is same on both lines because borrower might want new address managing new line
should be
	// we dont check borrower is the same on both lines because the borrower might want a new address managing the new line

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L62
    // ensure that line we are sending can accept them. There is no recovery option.
should be 
    // ensure that the line we are sending can accept it. There is no recovery option.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L71
   @notice - Forcefully take collateral from Escrow and repay debt for lender
should be
   @notice - Forcefully take collateral from Escrow and repay the debt for the lender

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L72
            current implementation just sends "liquidated" tokens to Arbiter to sell off how the deem fit and then manually repay with DepositAndRepay
should be 
            The current implementation just sends the "liquidated" tokens to the Arbiter to sell the, off how they deem fit and then manually repay the lender with DepositAndRepay

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L110
    @return isInsolvent - if the entire Line including all collateral sources is fuly insolvent.
should be 
    @return isInsolvent - if the entire Line including all collateral sources are fuly insolvent.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L23
 	@notice requires this Line is owner of the Escrowed collateral else Line will not init
should be 
 	@notice requires this Line is the owner of the Escrowed collateral or else the Line will not init

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L32
    @notice returns LIQUIDATABLE if Escrow contract is undercollateralized, else returns ACTIVE
should be 
   @notice returns LIQUIDATABLE if the Escrow contract is undercollateralized, otherwise it returns ACTIVE

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L73
   @notice require all collateral sold off before declaring insolvent
should be 
   @notice require that all collateral has been sold off before declaring insolvent

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L84
   @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
should be 
   @notice The helper function that allows the borrower to easily switch collateral to a new Line after repyment

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L59
     @notice - Claims revenue tokens from the Spigot (push and pull payments) and escrows them for the Owner withdraw later.
should be 
     @notice - Claims revenue tokens from the Spigot (push and pull payments) and escrows them for the Owner too withdraw later.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L61
               - Automatically sends portion to Treasury and then escrows Owner's share
should be 
               - Automatically sends a portion to the Treasury and then escrows the Owner's share

### Remove unused/commented out imports
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L8
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L10
// import { SecuredLine } from "./SecuredLine.sol";


### No SPDX license 
All files under scope have no SPDX license consider adding this to the project fles before live deployment to inform the general public under what license the project files can be used, currently it is arbituary as to whether someone may use the project files to write thier own project based on a similar structure, currently it is unclear if the files may be used by another party or not to create a project of thier own.

### Unused/empty recieve function 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L227

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert  (e.g. require(msg.sender == address(weth)))

There is also currently no other way to retrieve any over sent Eth from the contract either. consider doing something with this function such as using it to call another or put a way of withdrawing any oversent Eth into the contract also so that funds cannot be locked. it may be possble user sends more funds than intended and would not be able to simply retrieve them back

### Missing event on critical address change

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L223

Events help non contract tools track changes, dapps track changes, inform users of changes so they do not get suprised by changes in the above code it is possible to add a spigot which requires mutual consent between lender and borrow, no event is emited for this change consider adding an event to this function so that an update is sent out with regards to the address change for both lender and borrow (possibly emiting the new address)

### Incomplete natspec 

Icomplete natspec can make the code hard to decifer and understand exaclty what the intention is of certain functions, variables etc consider adding the missing return @ comments to these functions in particular

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L22
(consider adding to natspec @return LineLib.STATUS.ACTIVE)

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L126
(consider adding to natspec @return revenueContract, data)


### Require/Revert no descriptive reasons/strings

Require statements and reverts should contain some kind of descriptive reason string "I Fudged because" but the following contracts to do no have any so users will be half in the dark as to why the following functions revert if they do happen too, consider adding a reason srting to explain what exactly the user did wrong to cause the revert, this also makes it easier for a user to correct a mistake, takes out so,e guess work for them removes a bit of human error.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol
lineofcredit.sol 114, 243, 261, 328, 359, 394, 

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol
spigotedline 62, 103, 141, 143, 160, 239,

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol
Escrowline.sol 64, 90

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol
Escrowlib.sol 91, 105, 161, 198, 216

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol
spigotlib.sol 128 155, 180, 189, 198, 

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol
lineFactorylib.sol 73


### Lack of modifier/access controls

securedline.sol line80 is wrapped in a require statement for only arbiter, which according to the devs comments only the arbiter should be able to call this function, how ever the contract does not contain a modifier such as :-

    modifier onlyArbiter() {
        if(msg.sender != Arbiter) { revert CallerAccessDenied(); }
        _;
    }
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L80

The function itself liquidate line80 is wrapped in a require statement if(msg.sender != arbiter) { revert CallerAccessDenied(); } put into place on this function the access control and a modifier so that it is protected from anyone other than arbiter calling it.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L85

spigot.sol line85 has multiple functions such as claimescrow which as per the devs comments   * @dev - callable by `owner` how ever none of the functions have access control to check who it is calling them, no require statment to check the calling account nor is there a owner modifier within the contract itself so currently all of these functions are callable by anyone.
Add a owner modifier as defined above for securedline.sol and the wrap these functions in a require statement require(msg.sender == Owner, "not owner") to protect them from being called by an account that isnt supposed to have access as well as having a descriptive reason for reverting informing the user of a reason such as "only owner" 

currently anyone can call any of the functions even though the intention is for owner to call them

### Constructors with lack of address(0) checks

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L20
lack of 0 address checks on constructors LineFactory.solmakes use of 0 address checks in the constructor how ever the project has a lack of these checks in other areas such as :-

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L16
Escrowline.sol line16 The constructor takes an address arguement but there is no 0 address check meaning that it coud be possible for it to have a 0 address, add an != address(0) check to this constructor to mitigate the risk of a 0 address being set.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L31
spigot.sol line 31 The constructor takes 3 address arguements but there are no 0 address check meaning that it coud be possible for it to have  0 address's set for owner, treasuery, operator, add an != address(0) check to this constructor to mitigate the risk of a 0 address being used

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L42
escrow.sol line42 the constructor takes 3 address arguements but there are no 0 address check meaning that it coud be possible for it to have  0 address's set for _oracle, _line, _borrower, add an != address(0) check to this constructor for each address to mitigate the risk of a 0 address being used.

### Inconsistant use of pragma ^ 
Within the projects contracts all other contract use 0.8.9 securedline.sol, spigotedline.sol, lineofcredit.sol use ^0.8.9 consider specifying the pragma version in the same way for all files under scope.

### Lack of address(0) check on spigot.sol addition
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L125

spigot.sol line125 function allows the addition of a spigot, although the address cannot be address(this),   * @dev - revenueContract cannot be address(this), it is possible due to a lack of 0 address check to set it to a 0 address, add a 0 address check to this function to ensure it isnt set to a 0 address, also add a function modifier so that  * @dev - callable by `owner` is true, currently due to a lack of checks anyone can call this function.

   function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
        return state.addSpigot(revenueContract, setting);
    }