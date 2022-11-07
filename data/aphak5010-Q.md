# Debt DAO Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-00      | Missing zero address checks | - | 9 |
| L-01      | Comment / Docs not according to logic | - | 2 |
| L-02      | Implement 2-Step-Process for assigning roles | - | 2 |
| N-00      | Unlocked pragma | - | 5 |
| N-01      | Missing SPDX License Identifiers | - | 17 |
| N-02      | Variable `registry` can be immutable | Oracle.sol | 1 |
| N-03      | Function `getLatestAnswer` can be declared `view` | Oracle.sol | 1 |
| N-04      | Event is missing indexed fields | - | 8 |
| N-05      | Broken link in comment | MutualConsent.sol | 1 |
| N-06      | Use scientific notation for powers of ten | - | 2 |
| N-07      | Unreachable code | SpigotedLineLib.sol | 1 |

## [L-00] Missing zero address checks
There are 9 instances of missing zero address checks.  
I will list all of them with a quick description of the impact.  

### _operator variable in Spigot constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L37](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L37)  
If the operator is accidentally set to the zero address and the `removeSpigot` function is called, access to the RevenueContract will be lost.

### _treasury variable in Spigot constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L38](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L38)  
If the treasury is accidentally set to the zero address, revenue will be transferred to the zero address.  
This means the revenue will be lost.  

### _owner variable in Spigot constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L36](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L36)  
If the owner is accidentally set to the zero address, access to the Spigot will be lost.

### operator variable in SpigotLib.removeSpigot function
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L143](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L143)  
This function transfers ownership of the revenueContract to the operator.  
If the operator is the zero address, access to the revenueContract is lost.

### oracle_ variable in LineOfCredit constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L50](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L50)  
If the oracle is set to the zero address, no token values can be calculated.  

### arbiter_ variable in LineOfCredit constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L51](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L51)  
If the arbiter is set to the zero address, the LineOfCredit cannot be declared insolvent.

### borrower_ variable in LineOfCredit constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L52](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L52)  
The borrower should obviously not be the zero address.  

### _borrower variable in Escrow constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L46](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L46)  
If the borrower is set to the zero address collateral can be added but not released.

### _oracle variable in Escrow constructor
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L44](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L44)  
If the oracle is set to the zero address, no token values can be calculated. 

## [L-01] Comment / Docs not according to logic
### Transfer ownership function
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L187-L189](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L187-L189)  
The comment states that the "transfer ownership" function can be whitelisted.  
But the `SpigotLib.operate` function does not allow to call the `transferOwnerFunction`: [https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L73](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L73)

### Update treasury function
In the documentation it is stated that the `treasury` is simply an address that receives funds and that it has "no ability to interact with the Spigot" ([https://docs.debtdao.finance/products/the-spigot/spigot-roles/treasury](https://docs.debtdao.finance/products/the-spigot/spigot-roles/treasury)).  
However the `treasury` can call the `Spigot.updateTreasury` function ([https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L181](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L181)) and set the `treasury` to a new address. So it can obviously interact with the Spigot.

## [L-02] Implement 2-Step-Process for assigning roles
A 2-Step-Process should be used for assigning roles that are critical to the operation of the contract.  
E.g. if a wrong `owner` is set in the SpigotLib contract, access to the contract is lost.  

Instances:

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L178](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L178)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L187](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L187)  

For each instance of this issue, implement a `updateRole` function that sets a `pendingRole` address variable.  
The `pendingRole` address must then call a `claimRole` function to actually claim the role.


## [N-00] Unlocked pragma
Some of the files in scope use `0.8.9` as the Solidity compiler version.
Others use `^0.8.9`, meaning that the file will compile with versions `>=0.8.9` and `<0.9.0`.  
It is considered best practice to use a locked Solidity version, thereby only allowing compilation with a specific version.  
So the instances of `^0.8.9` should be changed to `0.8.9`.

There are 5 instances of this.  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L1)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L1)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L1)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L2](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L2)  

## [N-01] Missing SPDX License Identifiers
It is best practice to include a SPDX License Identifier in each file.

The Solidity documentation (https://docs.soliditylang.org/en/v0.6.8/layout-of-source-files.html#spdx-license-identifier) states:  
> Trust in smart contract can be better established if their source code is available.  
> Since making source code available always touches on legal problems  
> with regards to copyright, the Solidity compiler encouranges the use of  
> machine-readable SPDX license identifiers.  
> Every source file should start with a comment indicating its license  

Of all files in scope, only `Oracle.sol` includes one.  
So a SPDX License Identifier should be added to all the remaining 17 files.

## [N-02] Variable `registry` can be immutable
The `registry` variable in `Oracle.sol` is only assigned once in the constrcutor.  
So it can be `immutable`.

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L14](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L14)  

## [N-03] Function `getLatestAnswer` can be declared `view`
The function `getLatestAnswer` in `Oracle.sol` does not modify any state.  
So it can be declared `view`.

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L22](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L22)  

## [N-04] Event is missing indexed fields
Each event should use three indexed fields if it has three or more fields.  

There are 8 instances of events that do not have 3 indexed fields.  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L13](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L13)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L15](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L15)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L21](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L21)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L23](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L23)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L22](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L22)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L241](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L241)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L255](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L255)  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L262](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L262)  

## [N-05] Broken link in comment
The file `MutualConsent.sol` contains a comment with a link to the original file that this file is forked from.  
However the link is broken and returns a `404 Not Found` error code.  

[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L1)

## [N-06] Use scientific notation for powers of ten
`1e4` instead of `10000`  
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L9](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L9)  

`1e5` instead of `10**5`  
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42)  

## [N-07] Unreachable code
The `return false;` statement can never be reached since the line above is a `revert`.  
[https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L234](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L234)  

