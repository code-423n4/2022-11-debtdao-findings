## QA Report - low risk

### Unused/empty `receive()/fallback()` function
Leaving the `receive()` below without a `revert` could result in a loss of funds for a user who sends Ether to the contract (having no way to get anything back)

[SpigotedLine.sol: L271-272](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L271-L272)
```solidity
    // allow claiming/trading in ETH
    receive() external payable {}
```
___
___


### Missing checks for address(0x0) when assigning values to address state variables
___
Checks for address(0x0) are missing for `arbiter_` and `borrower_`:

[LineOfCredit.sol: L56-57](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L56-L57)
```solidity
        arbiter = arbiter_;
        borrower = borrower_;
```
Similarly for the following:

[Escrow.sol: L49-50](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L49-L50)
___
___


### `Solidity pragma` version should be upgraded to latest available version before finalization

The current solidity version in contracts is `^0.8.9`, compared to the latest version of `^0.8.17`. Only the latest version receives security fixes. However, the latest version often has bugs, so it's safer to use releases a few versions older at first, as has been done here.

However, some `Debt DAO` contracts use `pragma solidity 0.8.9;` rather than `pragma solidity ^0.8.9;`. These should be corrected:


[EscrowedLine.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L1)

[Spigot.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L1)

[Escrow.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L1)

[LineFactory.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L1)

[CreditLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L1)

[LineLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineLib.sol#L1)

[CreditListLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L1)

[EscrowLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L1)

[SpigotLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L1)

[SpigotedLineLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L1)

[MutualConsent.sol: L3](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/MutualConsent.sol#L3)

[LineFactoryLib.sol: L1](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L1)

```solidity
pragma solidity 0.8.9;
```
Change to `pragma solidity ^0.8.9;`
___
___


## QA Report: non-critical

### Typos
___
[LineOfCredit.sol: L37](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L37)
```solidity
    // Line Financials aggregated accross all existing  Credit
```
Change `accross` to `across`
___
The same typo (`priviliges`) occurs in both lines referenced below:

[LineOfCredit.sol: L45](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L45)

[SpigotedLine.sol: L46](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L46)
```solidity
   * @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.
```
Change `priviliges` to `privileges` in both cases
___
The same typo (`diferent`) occurs in both lines referenced below:

[LineOfCredit.sol: L107](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L107)

[LineOfCredit.sol: L416](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L416)
```solidity
     * @dev - updates `status` variable in storage if current status is diferent from existing status
```
Change `diferent` to `different` in both cases
___
The same typo (`demoninated`) occurs in both lines referenced below:

[LineOfCredit.sol: L213](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L213)

[CreditLib.sol: L250](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L250)
```solidity
      @notice - accrues token demoninated interest on a lender's position.
```
Change `demoninated` to `denominated` in both cases
___
[LineOfCredit.sol: L395](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L395)
```solidity
        // ensure all money owed is accounted for. Accrue facility fee since prinicpal was paid off
```
Change `prinicpal` to `principal`
___
[LineOfCredit.sol: L501](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L501)
```solidity
        // If all credit lines are closed the the overall Line of Credit facility is declared 'repaid'.
```
Remove repeated word `the`
___
[LineOfCredit.sol: L510](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L510)
```solidity
     * @notice - Insert `p` into the next availble FIFO position in the repayment queue
```
Change `availble` to `available`
___
[SpigotedLine.sol: L38](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L38)
```solidity
     * @dev    - private variable so other Line modules do not interfer with Spigot functionality
```
Change `interfer` to `interfere`
___
[SpigotedLine.sol: L84](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L84)
```solidity
      * @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent
```
Change `itselgf` to `itself`
___
The same typo (`priviliegad`) occurs in all four lines referenced below:

[SpigotedLine.sol: L85](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L85)

[EscrowedLine.sol: L45](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L45)

[EscrowedLine.sol: L74](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L74)

[EscrowedLine.sol: L85](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L85)

```solidity
      *(@dev priviliegad internal function.
```
Change `priviliegad` to `privileged` in each case
___
The same typo (`priviliged`) occurs in both lines referenced below:

[SpigotedLine.sol: L85](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L85)

[SpigotedLineLib.sol: L44](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L44)
```solidity
     * @dev     - priviliged internal function
```
Change `priviliged` to `privileged` in both cases
___
[SecuredLine.sol: L110](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L110)
```solidity
    * @return isInsolvent - if the entire Line including all collateral sources is fuly insolvent.
```
Change `fuly` to `fully`
___
[EscrowedLine.sol: L84](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L84)
```solidity
   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
```
Change `swithc` to `switch` and `repyment` to `repayment`
___
[Spigot.sol: L133](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L133)
```solidity
     * @dev - revenuContract's transfer func MUST only accept one paramteter which is the new owner.
```
Change `paramteter` to `parameter`
___
[Escrow.sol: L94](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L94)
```solidity
     * @notice - allows  the lines arbiter to  enable thdeposits of an asset
```
Change `thdeposits` to `the deposits`
___
[Escrow.sol: L95](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L95)
```solidity
     *        - gives  better risk segmentation forlenders
```
Change `forlenders` to `for lenders`
___
[InterestRateCredit.sol: L8](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L8)
```solidity
    // Must divide by 100 too offset bps in numerator and divide by another 100 to offset % and get actual token amount
```
Change `too` to `to`
___
[InterestRateCredit.sol: L17](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L17)
```solidity
     * @notice Interest rate / acrrued interest calculation contract for Line of Credit contracts
```
Change `acrrued` to `accrued`
___
The same typo (`interset`) occurs in both lines referenced below:

[CreditLib.sol: L123](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L123)

[CreditLib.sol: L237](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L237)
```solidity
    * @param oracle - interset rate contract used by line that will calculate interest owed
```
Change `interset` to `interest` in both cases
___
[CreditLib.sol: L200](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L200)
```solidity
    * @param credit - The lender position that is being bwithdrawn from
```
Change `bwithdrawn` to `being withdrawn`
___
[CreditLib.sol: L221](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L221)
```solidity
          // emit events before seeting to 0
```
Change `seeting` to `setting`
___
[SpigotedLineLib.sol: L48](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L48)
```solidity
     * @param spigot        - The Spigot to claim from. Must be owned by adddress(this)
```
Change `adddress` to `address`
___
[SpigotedLineLib.sol: L188](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L188)
```solidity
   * @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either 
```
Change `revenuw` to `revenue`
___
[SpigotedLineLib.sol: L189](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L189)
```solidity
                the Borrower (if a Line of Credit has been been fully repaid) or 
```
Remove repeated word `been`
___
___


### Event is missing `indexed` fields
Each `event` should use three `indexed` fields if there are three or more fields
___
[SpigotLib.sol: L255-260](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L255-L260)
```solidity
    event ClaimRevenue(
        address indexed token,
        uint256 indexed amount,
        uint256 escrowed,
        address revenueContract
    );
```
___
[SpigotLib.sol: L262-266](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L262-L266)
```solidity
    event ClaimEscrow(
        address indexed token,
        uint256 indexed amount,
        address owner
    );
```
___
___


### TODOs and other open items
Comments that refer to open items should be addressed and more fully explained, or else modified or removed. 
___
[LineFactory.sol: L140](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L140)
```solidity
        // TODO: test
```
___
[LineFactory.sol: L145](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L145)
```solidity
        // TODO: test
```
___
[CreditListLib.sol: L18](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L18)
```solidity
     * Bob - consider renaming this function removeId()
```
___
[SpigotLib.sol: L117](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L117)
```solidity
        self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?
```
___
___


### Use scientific notation for large multiples of 10 rather than decimal literals
For readability, used scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000) for large multiples of ten
___
[InterestRateCredit.sol: L6-11](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/interest-rate/InterestRateCredit.sol#L6-L11)
```solidity
    // 1 Julian astronomical year in seconds to use in calculations for rates = 31557600 seconds
    uint256 constant ONE_YEAR = 365.25 days;
    // Must divide by 100 too offset bps in numerator and divide by another 100 to offset % and get actual token amount
    uint256 constant BASE_DENOMINATOR = 10000;
     // = 31557600 * 10000 = 315576000000;
    uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;
```
Suggestion:
```solidity
    // 1 Julian astronomical year in seconds to use in calculations for rates = 3.15576e7 seconds
    uint256 constant ONE_YEAR = 365.25 days;
    // Must divide by 100 to offset bps in numerator and divide by another 100 to offset % and get actual token amount
    uint256 constant BASE_DENOMINATOR = 1e4;
     // = 3.15576e7 * 1e4 = 3.15576e11;
    uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;
```
___
___


### Extra-long single-line comments 
In theory, comments over 80 characters should wrap using multi-line comment syntax. Even if longer comments (up to 120 characters) are acceptable and a scroll bar is provided, very long comments can interfere with readability. 

Some of the long comments in `Debt DAO` do wrap but the treatment of very long comments is inconsistent. Also, long spaces within some comments elongate them unnecessarily. Below, I divide the long comments in the contest into three types, with examples of each and references to additional long comments of the type. 

___
__Type 1: Long NatSpec statements__

Example

[CreditListLib.sol: L12-13](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L12-L13)
```solidity
     * @dev assumes that `id` of a single credit line within the Line of Credit facility (same lender/token) is stored only once in the `positions` array 
     since there's no reason for them to be stored multiple times.
```
Suggestion:
```solidity
     * @dev assumes that `id` of a single credit line within the Line of Credit facility (same lender/token)   
       is stored only once in the `positions` array since there's no reason for them to be stored multiple times.
```
Below are more extra-long (at least 120 characters) NatSpec statements:

***LineOfCredit.sol***

[L42-43](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L42-L43), [L47](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L47)

***SpigotedLine.sol***

[L18](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L18), [L28](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L28), [L36-37](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L36-L37), [L49](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L49), [L50](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L50)

[L51](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L51), [L84](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L84), [L182](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L182)

***Spigot.sol***

[L12-13](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L12-L13), [L26](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L26), [L27](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L27), [L28-29](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L28-L29)

[L68](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L68), [L131-132](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L131-L132) 

***Escrow.sol***

[L17](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L17), [L36](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L36), [L38-39](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L38-L39), [L40](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L40)

[L62](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L62), [L63](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L63), [L71](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L71)

***LineFactory.sol***

[L171](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L171)

***CreditLib.sol***

[L31-32](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L31-L32), [L55](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L55), [L101](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L101)

***CreditListLib.sol***

[L17](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L17)

***SpigotedLineLib.sol***

[L39-42](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L39-L42), [L46](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L46), [L164](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L164), [L212-214](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L212-L214) 

***LineFactoryLib.sol***

[L34](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L34)
___

__Type 2: Extra-long lines that include both code and comments__

If a line containing a comment is longer than 120 characters, it makes sense to put the comment in the line above, as shown:

Example:

[LineOfCredit.sol: L35](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L35)

```solidity
    mapping(bytes32 => Credit) public credits; // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit
```
Suggestion:
```solidity
    // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit
    mapping(bytes32 => Credit) public credits;
```

Similarly for the following: 

[LineOfCredit.sol: L58](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L58)
___

__Type 3: Extra-long comment lines__

If a line containing a comment is longer than 120 characters, it makes sense to wrap the comment, as shown:

Example:

[CreditLib.sol: L42-43](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L42-L43)

```solidity
  /** Emits that a Borrower has repaid an amount of interest 
  (N.B. results in an increase in interestRepaid, i.e. interest not yet withdrawn by a Lender). There is no corresponding function
```
Suggestion:
```solidity
  /** Emits that a Borrower has repaid an amount of interest 
      (N.B. results in an increase in interestRepaid, i.e. interest not yet withdrawn by a Lender). 
      There is no corresponding function.
```

Similarly for the following: 

[CreditListLib.sol: L14](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L14)

[SpigotLib.sol: L15](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L15)

[SpigotLib.sol: L69](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L69)
___
___


### Strong language in comment
While a comment may contain abbreviations, shorthand for well-known or previously defined terms, inconsistent or nonstandard punctuation, and use of minor grammatical errors, it should not contain strong language that could be offensive. 
___
[SpigotLib.sol: L69](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L69)
```solidity
        // cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case
```
___
___


### Update sensitive terms in both comments and code
Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/).
___
[Spigot.sol: L187-195](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L187-L195)
```solidity
     * @notice - Allows Owner to whitelist function methods across all revenue contracts for Operator to call.
     *           Can whitelist "transfer ownership" functions on revenue contracts
     *           allowing Spigot to give direct control back to Operator.
     * @dev - callable by `owner`
     * @param func - smart contract function signature to whitelist
     * @param allowed - true/false whether to allow this function to be called by Operator
     */
     function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {
        return state.updateWhitelistedFunction(func, allowed);
```
Suggestion: Change `whitelist` to `allowlist` throughout. Similarly for its variants. 
___
___


### NatSpec is partially missing

NatSpec is partially missing for the following `external` or `public` `functions` (NatSpec is not required for `internal` or `private` functions).

Note that I'm excluding functions preceded by comments such as [/// see ILineOfCredit.increaseCredit](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L264). However, such references appear to be insufficient and inconvenient substitutes for full NatSpec for complex functions with multiple parameters.
___
[Spigot.sol: L144-148](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L144-L148)
```solidity
    // Changes the revenue split between the Treasury and the Owner based upon the status of the Line of Credit
    // or otherwise if the Owner and Borrower wish to change the split.
    function updateOwnerSplit(address revenueContract, uint8 ownerSplit)
        external
        returns(bool)
```
Missing: `@param revenueContract`, `@param ownerSplit` and `@return`
___
[Spigot.sol: L128-139](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L128-L139)
```solidity
    /**
     *   @dev   We don't transfer the ownership of Escrow and Spigot internally
     *          because they aren't owned by the factory, the responsibility falls
     *          on the [owner of the line]
     *   @dev   The `cratio` in the CoreParams are not used, due to the fact
     *          they're passed in when the Escrow is created separately.
     */
    function deploySecuredLineWithModules(
        CoreLineParams calldata coreParams,
        address mSpigot,
        address mEscrow
    ) external returns (address line) {
```
Missing: `@param coreParams`, `@param mSpigot`, `@param mEscrow` and `@return`
___
[EscrowLib.sol: L28-34](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L28-L34)
```solidity
    /**
     * @notice updates the cratio according to the collateral value vs line value
     * @dev calls accrue interest on the line contract to update the latest interest payable
     * @param oracle - address to call for collateral token prices
     * @return cratio - the updated collateral ratio in 4 decimals
    */
    function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {
```
Missing: `@param self`
___
[EscrowLib.sol: L46-51](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L46-L51)
```solidity
    /**
    * @notice - Iterates over all enabled tokens and calculates the USD value of all deposited collateral
    * @param oracle - address to call for collateral token prices
    * @return totalCollateralValue - the collateral's USD value in 8 decimals
    */
    function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256) {
```
Missing: `@param self`
___
[SpigotedLineLib.sol: L142-148](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L142-L148)
```solidity
    /**
     * @notice cleanup function when a Line of Credit facility has expired.
        Used in the event that we want to reuse a Spigot instead of starting from scratch
     */
    function rollover(address spigot, address newLine) external returns(bool) {
      require(ISpigot(spigot).updateOwner(newLine));
      return true;
```
Missing: `@param spigot`, `@param newLine` and `@return`
___
[LineFactoryLib.sol: L33-47](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L33-L47)
```solidity
    /**
      @notice sets up new line based of config of old line. Old line does not need to have REPAID status for this call to succeed.
      @dev borrower must call rollover() on `oldLine` with newly created line address
      @param oldLine  - line to copy config from for new line.
      @param borrower - borrower address on new line
      @param ttl      - set total term length of line
      @return newLine - address of newly deployed line with oldLine config
     */
    function rolloverSecuredLine(
        address payable oldLine,
        address borrower, 
        address oracle,
        address arbiter,
        uint ttl
    ) external returns(address) {
```
Missing: `@param oracle` and `@param arbiter` 
___

`@return` is the only type of NatSpec statement missing for the following `functions`:
___
[Spigot.sol: L101-109](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L101-L109)
```solidity
    /**
     * @notice - Allows Operator to call whitelisted functions on revenue contracts to maintain their product
     *           while still allowing Spigot Owner to receive its revenue stream
     * @dev - cannot call revenueContracts claim or transferOwner functions
     * @dev - callable by `operator`
     * @param revenueContract - contract to call. Must have existing settings added by Owner 
     * @param data - tx data, including function signature, to call contract with
     */
    function operate(address revenueContract, bytes calldata data) external returns (bool) {
        return state.operate(revenueContract, data);
```
`@return` similarly missing for the following `functions`:

***Spigot.sol***

[L118-125](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L118-L125), [L131-139](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L131-L139), [L154-159](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L154-L159), [L165-170](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L165-L170)

[L176-181](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L176-L181), [L187-194](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L187-L194), [L203-206](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L203-L206), [L211-216](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L211-L216)

***Escrow.sol***

[L54-57](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L54-L57), [L61-65](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L61-L65), [L69-74](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L69-L74), [L93-100](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/escrow/Escrow.sol#L93-L100)

***LineFactory.sol***

[L170-182](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L170-L182)

***LineLib.sol***

[L28-40](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineLib.sol#L28-L40), [L53-65](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineLib.sol#L53-L65), [L76-80](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineLib.sol#L76-L80)

___
___

### NatSpec is completely missing

NatSpec is completely missing for the following `external` or `public` `functions`: 

[LineOfCredit.sol: L64-67](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L64-L67)
```solidity
    function init() external virtual returns(LineLib.STATUS) {
      if(status != LineLib.STATUS.UNINITIALIZED) { revert AlreadyInitialized(); }
      return _updateStatus(_init());
    }
```
NatSpec is similarly missing for the following `functions`:

***SpigotedLine.sol***

[L78-80](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L78-L80)

***Spigot.sol***

[L41-43](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L41-L43), [L45-47](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L45-L47), [L49-51](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L49-L51), [L220-222](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/spigot/Spigot.sol#L220-L222)

***LineFactory.sol***

[L59-61](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L59-L61), [L87-89](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L87-L89)

***SpigotedLineLib.sol***

[L120-127](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L120-L127), [L151](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L151)

***LineFactoryLib.sol***

[L57](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L57), [L77-86](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L77-L86)


NatSpec is completely missing for the following `constructor`:

[SecuredLine.sol: L13-22](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SecuredLine.sol#L13-L22)
```solidity
    constructor(
        address oracle_,
        address arbiter_,
        address borrower_,
        address payable swapTarget_,
        address spigot_,
        address escrow_,
        uint ttl_,
        uint8 defaultSplit_
    ) SpigotedLine(
```
NatSpec is similarly missing for the following `constructors`:

[EscrowedLine.sol: L16-17](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/EscrowedLine.sol#L16-L17)

[LineFactory.sol: L20-25](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/factories/LineFactory.sol#L20-L25)
___

NatSpec is completely missing for the following `struct`:

[EscrowLib.sol: L12-19](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L12-L19)
```solidity
struct EscrowState {
    address line;
    address[] collateralTokens;
    /// if lenders allow token as collateral. ensures uniqueness in collateralTokens
    mapping(address => bool) enabled;
    /// tokens used as collateral (must be able to value with oracle)
    mapping(address => IEscrow.Deposit) deposited;
}
```
NatSpec is similarly missing for the following `struct`:

[SpigotLib.sol: L7-17](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L7-L17)
___

NatSpec is completely missing for the following `event`:

[CreditLib.sol: L16-21](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditLib.sol#L16-L21)
```solidity
    event AddCredit(
        address indexed lender,
        address indexed token,
        uint256 indexed deposit,
        bytes32 id
    );
```
NatSpec is similarly missing for the following `events`:

***EscrowLib.sol***

[L221](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L221), [L223](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L223), [L225](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L225), [L227](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L227)

***SpigotLib.sol***

[L241-244](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L241-L244), [L246](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L246), [L248](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L248), [L250-252](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L250-L252), [L255-260](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L255-L260)

[L262-266](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L262-L266), [L270](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L270), [L272](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L272), [L274](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L274)

***SpigotedLineLib.sol***

[L30-34](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L30-L34)

***MutualConsent.sol***

[L21-23](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/MutualConsent.sol#L21-L23)

***LineFactoryLib.sol***

[L8-14](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L8-L14), [L16-21](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L16-L21), [L23-28](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineFactoryLib.sol#L23-L28)
___
___


### Missing `require` error message
A `require` message should be included to enable users to understand the reason for failure. 

Use Custom Errors to add an explanatory message to each of the `require` statements referenced below

[LineOfCredit.sol: L112](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L112)
```solidity
        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
```
Similarly for the following `require` statements: 

***LineOfCredit.sol***

[L241](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L241), [L259](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L259), [L326](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L326)

***SpigotedLine.sol***

[L62](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L62), [L143](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L143), [L160](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L160), [L239](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L239)

***EscrowedLine.sol***

[L64](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/EscrowedLine.sol#L64), [L90](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/EscrowedLine.sol#L90)

***EscrowLib.sol***

[L91](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L91), [L105](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L105), [L161](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L161), [L198](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L198), [L216](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L216)

***SpigotLib.sol***

[L96](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L96), [L128](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L128),  [L130](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L130), [L155](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L155) 

[L180](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L180), [L189](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L189), [L201](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L201) 

***SpigotedLineLib.sol***

[L147](https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L147)
___
___
