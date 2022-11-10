# QA

# Low
## Prevent div by 0
### Impact
On several locations in the code precautions are not being taken to not divide by `0`, this would revert the code.

### Proof of Concept
Navigate to the following contracts,
- `decimals` can be 0 and therefore would revert for tokens with 0 decimals
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L117
    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);

### Recommended Mitigation Steps
Recommend making sure division by `0` won’t occur by checking the variables beforehand and handling this edge case.

## Unused `receive()` function will lock ether in contract
### Summary
If the intention is for the Ether to be used, the function should call another function, otherwise it should `revert`

### Github Permalinks


https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L272
    receive() external payable {}

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L227
    receive() external payable {


### Mitigation
If the intention is for the Ether to be used, the function should call another function, otherwise it should `revert`.



## Missing checks for address(0x0) when assigning values to `address` state or `immutable` variables 
### Summary
Zero address should be checked for state variables, immutable variables. A zero address can lead into problems.
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L54-L58
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L26
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/oracle/Oracle.sol#L16
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L49-L51
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L14-L19
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L50-L52
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L16-L18
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L36-L38

### Mitigation
Check zero address before assigning or using it







## block.timestamp used as time proxy 
### Summary
Risk of using `block.timestamp` for time should be considered. 
### Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.
### References
SWC ID: 116

### Github Permalinks 
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L132
        if (block.timestamp >= deadline && count > 0) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L58
        deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L48
        uint256 timespan = block.timestamp - rate.lastAccrued;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L50
        rates[id].lastAccrued = block.timestamp;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L82
            lastAccrued: block.timestamp





### Mitigation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


## Front run initializer
### Summary
The initialize function that initializes important contract state can be called by anyone.
### Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L64-L67


### Mitigation
Use the constructor to initialize non-proxied contracts.

## Return value not being checked 
### Details
Return values not being checked may lead into unexpected behaviors with functions. Not events/Error are being emitted if that fails, so functions would be called even of not being working as expect.

### Github Permalinks
slitherNotChecked


### Mitigation
Check values and `revert`/`emit` events if needed




# Informational
##  Use of magic numbers is confusing and risky
### Summary
Magic numbers are hardcoded numbers used in the code which are ambiguous to their intended purpose. These should be replaced with constants to make code more readable and maintainable.
### Details
Values are hardcoded and would be more readable and maintainable if declared as a constant

### Github Permalinks
- `10`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L117
- `10` and `5`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L42-L43
- `100`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L90

### Mitigation
Replace magic hardcoded numbers with declared constants.

## Naming convention of constants
### Summary
Constant naming convention is all upper case. 
### Details
Some constants are not using proper style.
Constant should be in `UPPER_CASE_WITH_UNDERSCORES` as per Solidity Style Guide. 
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L12
    uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L14
    uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio

### Mitigation
Rename the constant to uppercase style: `CONSTANTS_WITH_UNDERSCORES`. 




## Different versions of pragma
### Summary
Some of the contracts include an unlocked pragma, e.g., pragma solidity >=0.8.9. 

Locking the pragma helps ensure that contracts are not accidentally deployed using an old compiler version with unfixed bugs.

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L1
pragma solidity ^0.8.9;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L1
pragma solidity ^0.8.9;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L1
pragma solidity ^0.8.9;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/oracle/Oracle.sol#L2
pragma solidity ^0.8.9;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L1
pragma solidity ^0.8.9;


### Mitigation
Lock pragmas to a specific Solidity version. 
Consider converting ^ 0.8.9 into 0.8.9]




## Warning SPDX-License missing
### Summary
SPDX license should be included for avoiding compiler warnings.

### Details
Compilation warnings/errors on [    ]:
Warning: SPDX license identifier not provided in source file. Before publishing, consider adding a comment containing "SPDX-License-Identifier: <SPDX-License>" to each source file. Use "SPDX-License-Identifier: UNLICENSED" for non-open-source code. Please see https://spdx.org for more information.

### Github Permalink
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/LineLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/MutualConsent.sol
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/LineFactoryLib.sol

### Mitigation
Add a SPDX License to each file



## Bad order of code
### Summary
Clearness of the code is important for the readability and maintainability.
As Solidity guidelines says about declaration order:
1.Type declarations
2.State variables
3.Events
4.Modifiers
5.Functions
Also, state variables order affects to gas in the same way as ordering structs for saving storage slots

### Github Permalink
- modifiers after functions
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L74-L103

- modifier after constructor
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L23-L31

- events and errors after functions
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L239-L289
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L221-L235


### Mitigation
Follow solidity style guidelines https://docs.soliditylang.org/en/v0.8.15/style-guide.html




## Missing Natspec 
### Summary 
Missing Natspec and regular comments affect readability and maintainability of a codebase. 
### Details 
Contracts has partial or full lack of comments
### Github Permalinks 
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L86-L219
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L16-L427
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L1-L290
https://github.com/debtdao/Line-of-Credit/blob/c1037d7f873cfb2af48fdaf00e44cd55c7737fc1/contracts/utils/SpigotedLineLib.sol#L141-L235
https://github.com/debtdao/Line-of-Credit/blob/e7fa299645f7ae857876577aa7eda924d52f8516/contracts/utils/MutualConsent.sol#L1-L68
https://github.com/debtdao/Line-of-Credit/blob/ef130955057d2db4e73a4d8330bf65282a13aa02/contracts/utils/LineFactoryLib.sol#L56-L88
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L72-L273

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L12-L104

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L20-L40
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L41-L229
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/oracle/Oracle.sol#L1-L35
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L1-L87
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L1-L194

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L1-L263
https://github.com/debtdao/Line-of-Credit/blob/d7ef66035ddf873b0c96804a1c9deeebb1f798ea/contracts/utils/LineLib.sol#L1-L87
 ### mitigation
 - Add @param descriptors
 - Add @return descriptors
 - Add Natspec comments. 
 - Add inline comments. 
 - Add comments for what the contract does


## Missing initializer modifier on constructor
### Impact
OpenZeppelin [recommends](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5) that the initializer modifier be applied to constructors in order to avoid potential griefs, [social engineering](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/4), or exploits. 

### Github Permalink
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L49-L62


### Mitigation
Ensure that the modifier is applied to the implementation contract. If the default constructor is currently being used, it should be changed to be an explicit one with the modifier applied.



## Open TODOs   
### Summary
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment
### Details
The code includes a `TODO` that affects readability and focus on the readers/auditors of the contracts
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L140
        // TODO: test

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L145
        // TODO: test

### Mitigation
Remove already done `TODO`




## Inconsistent spacing in comments
Some lines use // x and some use //x. The instances below point out the usages that don't follow the majority, within each file

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L58
        deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility

## Maximum line length exceeded
### Summary
Long lines should be wrapped to conform with Solidity Style guidelines. 
### Details 
Lines that exceed the 79 (or 99) character length suggested by the Solidity Style guidelines. Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length
### Github Permalinks 

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L31

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L35

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L42

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L43

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L45

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L47

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L58

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L107

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L133

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L416

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L501

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L524

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L18

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L28

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L31

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L36

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L37

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L43

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L46

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L49

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L50

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L51

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L84

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L182

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L58

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SecuredLine.sol#L72

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L84

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L12

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L13

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L14

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L25

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L26

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L27

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L28

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L59

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L63

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L67

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L68

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L101

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L131

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L133

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L144

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/spigot/Spigot.sol#L187

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L17

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L23

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L35

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L36

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L38

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L40

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L62

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L63

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L71

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/escrow/Escrow.sol#L118

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/oracle/Oracle.sol#L10

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/oracle/Oracle.sol#L11

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L8

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L69

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L171

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L12

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L23

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L26

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L31

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L43

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L55

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L101

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/LineLib.sol#L12

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L8

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L12

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L14

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L16

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L17

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L35

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L34

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L47

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L51

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L104

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L182

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L187

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L210

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L13

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L15

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L24

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L29

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L39

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L42

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L48

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L61

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L69

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L83

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L125

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L187

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L196

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L208

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L39

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L40

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L41

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L42

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L46

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L164

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L169

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L175

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L188

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L194

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L212

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L213

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L217

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/MutualConsent.sol#L1

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/MutualConsent.sol#L28

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/LineFactoryLib.sol#L34

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/LineFactoryLib.sol#L87
### Mitigation
Reduce line length to less than 99 at least to improve maintainability and readability of the code 




## Large multiples of ten should use scientific notation (e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability
### Summary
Multiples of 10 can be declared as constants with scientific notation so it's easier to read them and less prone to miss/exceed a 0 of the expected value.

### Details
Value 10000 can be used in scientific notation
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L9
    uint256 constant BASE_DENOMINATOR = 10000;

### Mitigation
Replace hardcoded numbers with constants that represent the scientific corresponding notation

## Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)
### Summary
Multiples of 10 can be declared as constants with scientific notation

### Details
Value 10**5 can be used in scientific notation
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L42
        uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals


### Mitigation
Consider replacing long 0 numbers and 10**value notation to scientific notation




## State variables that do not change should be constant and written in UPPERCASE
### Summary
`constant` keyword helps with readability of the code and to make sure that they do not change. 

### Details
Code contains state variables that do not change and so they can be declared `constant`

### Github Permalinks
0xNotConstants


### Mitigation
Add constant and change [[[`MaxPeriod` to `MAX_PERIOD`]]]




## Missing error messages in require statements
### Summary
Require/revert statements should include error messages in order to help at monitoring the system.
### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L112
        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L241
        require(interestRate.setRate(id, drate, frate));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L259
        require(interestRate.setRate(id, drate, frate));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L326
        require(amount <= credit.principal + credit.interestAccrued);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L62
        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L143
      require(amount <= unusedTokens[credit.token]);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L160
        require(msg.sender == borrower);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L239
        require(msg.sender == arbiter);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L64
    require(escrow_.liquidate(amount, targetToken, to));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L90
    require(escrow.updateLine(newLine));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L26
        require(

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L91
        require(amount > 0);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L105
        require(msg.sender == ILineOfCredit(self.line).arbiter());

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L161
        require(amount > 0);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L198
        require(amount > 0);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L216
      require(msg.sender == self.line);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L96
            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L128
        require(revenueContract != address(this));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L130
        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L155
        require(success);

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L180
        require(newOwner != address(0));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L189
        require(newOperator != address(0));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L201
        require(newTreasury != address(0));

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L147
      require(ISpigot(spigot).updateOwner(newLine));
### Mitigation
Add error messages 

## Inconsistent spacing in for loops
### Impact
For loops are recommended to be declared such as `for (`, however, sometimes are declared as `for(`
https://docs.soliditylang.org/en/v0.8.16/style-guide.html#control-structures

### Github Permalinks
- `for(` this are missing a space
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L23
      for(uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L51
        for(uint i = 1; i < len; ++i) {

- `for (`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L179
        for (uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L203
        for (uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L520
        for (uint256 i; i <= lastSpot; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L57
        for (uint256 i; i < length; ++i) {

### Mitigation
Add a space between the `for` and the `(`
