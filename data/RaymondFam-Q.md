## Unspecific Compiler Version Pragma
For most source-units the compiler version pragma is very unspecific ^0.8.9. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different EVM compilation that is ultimately deployed on the blockchain.

Avoid floating pragmas where possible. It is highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.

## Sanity/Threshold/Limit Checks
Devoid of sanity/threshold/limit checks, critical parameters can be configured to invalid values, causing a variety of issues and breaking expected interactions within/between contracts. Consider adding proper `uint256` validation as well as zero address checks in the constructor. A worst case scenario would render the contract needing to be re-deployed in the event of human/accidental errors that involve value assignments to immutable variables. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values. These checks are generally not implemented in all of the in scope contracts.

## Decimal Used in Constant Assignment
There is no floating point in Solidity. You should keep numbers in whole number format. The following instance of constant assignment should be refactored as follows:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L7

```
    uint256 constant ONE_YEAR = 365 days + 6 hours;
```
## Missing Require Error Message
Consider adding a less than 32 character string message to all require statements just so that a relevant message would be displayed in the event of a revert. The error message is generally not included in all of the in scope contracts.

## `block.timestamp` Unreliable
The use of `block.timestamp` as part of the time checks can be slightly altered by miners/validators to favor them in contracts that have logic strongly dependent on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.

Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L48-L50
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L82

## Function Calls in Loop Could Lead to Denial of Service
Function calls made in unbounded loop are error-prone with potential resource exhaustion as it can trap the contract due to the gas limitations or failed transactions. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520

Consider bounding the loop where possible to avoid unnecessary gas wastage and denial of service.

## Typo Errors
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520

```
            @ priviliges
Line 45   * @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84

```
            @ swithc ... repyment
   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L85

```
            @ priviliegad
   *(@dev priviliegad internal function.
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L94

```
            @ thdeposits
     * @notice - allows  the lines arbiter to  enable thdeposits of an asset
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221

```
            @ seeting
          // emit events before seeting to 0
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L200

```
            @ bwithdrawn
    * @param credit - The lender position that is being bwithdrawn from
```

## Inadequate NatSpec
Solidity contracts can use a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more. Please visit the following link for further details:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L64-L72
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L117-L210
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L223-L409

## Variable Names
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L123
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L185
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L55
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L48-L50

## Unused `receive()` Will Lock Ether in Contract
If the Ether received is intended to be used, the function should call another function, otherwise it should revert. Here is one instance entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272

## Lack of Events for Critical Operations
Critical operations not triggering events will make it difficult to review the correct behavior of the deployed contracts. Users and blockchain monitoring systems will not be able to detect suspicious behaviors at ease without events. Consider adding events where appropriate for all critical operations for better support of off-chain logging API. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L86

## `constant` Variables Should be Capitalized
Constants should be named with all capital letters with underscores separating words if need be. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L14

## Use `delete` to Clear Variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with delete statements. Here are some the three instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L188
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L219
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L117

## Missing `payable` Visibility
`receiveTokenOrETH()` serves to receive ETH or ERC20 token at this contract from an external contract according to the comment and the function logic. However, devoid of the `payable` visibility, all calls attempting to send ETH into the contract via this function call is going to be rejected.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L59-L74

## Constant Expression
To enhance readability whilst reducing human error, the following instance of constant should be assigned using a standard formula:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L25-L26

```
    uint256 constant MAX_INT = type(uint256).max
```
Note: `2**256 - 1` and `uint256(-1)` are two other alternatives that can used for the constant assignment. Additionally, the literally long numerical value may be commented if need be.

## Minimization of Truncation
As an example, the code line below may be refactored to minimize the frequency of truncation:  

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L43

```
        return (_numerator + debtValue * 5) / (10 * debtValue);
```
Note: Comment the refactored code with the original multiple division arithmetic operation where deemed fit.

## Events Associated With Setter Functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178-L205

## Two-step Change
In `SpigotLib.sol`, `updateOwner()` should be implemented transferring owner role to another address in a pending state. And then, `acceptOwner()` should be introduced for the newly nominated owner to claim the new role. This will ensure the new owner is going to be fully aware of the privilege assigned/transferred. Additionally, the risk of having ownership transferred to an invalid address that would cause the contract to be without an owner will be avoided. The same adoption should also be applied to `updateOperator()` and `updateTreasury()` where possible.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178-L205

## Failed Function Call Could Occur Without Contract Existence Check
Performing a low-level calls without confirming contract’s existence (not yet deployed or have been destructed) could return success even though no function call was executed. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L58-L65

## Lines Too Long
Lines in source code are typically limited to 80 characters, but it’s reasonable to stretch beyond this limit when need be as monitor screens theses days are comparatively larger. Considering the files will most likely reside in GitHub that will have a scroll bar automatically kick in when the length is over 164 characters, all code lines and comments should be split when/before hitting this length. Keep line width to max 120 characters for better readability where possible. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L35
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L43
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L47
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L36-L37
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L72
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L62
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L55
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L101

## Add a Timelock to Critical Parameter Change
It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes. Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125-L213

## Contract Owner Has Too Many Privileges
The owner of the contracts has too many privileges relative to standard users. The consequence is disastrous if the contract owner's private key has been compromised. And, in the event the key was lost or unrecoverable, no implementation upgrades and system parameter updates will ever be possible.

For a project this grand, it increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure; and, here are some of the incidents that could possibly transpire:

Transfer ownership and mess up with all the setter functions, hijacking the entire protocol.

Consider:
1) splitting privileges (e.g. via the multisig option) to ensure that no one address has excessive ownership of the system,
2) clearly documenting the functions and implementations the owner can change,
3) documenting the risks associated with privileged users and single points of failure, and
4) ensuring that users are aware of all the risks associated with the system.

## Open TODOs
Open TODOs can point to architecture or programming issues that still need to be resolved. Consider resolving them before deploying.

Here are some of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145

## Complementary Codehash Check for Immutable Address
Consider adding an optional codehash check for immutable address at the constructor since the zero address check cannot guarantee a matching address has been inputted. As an example, the following code block could be refactored to:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L20-L39

```
    constructor(
        address moduleFactory,
        address arbiter_,
        address oracle_,
        address swapTarget_,
        bytes32 arbiter_CodeHash,
        bytes32 oracle_CodeHash,
        bytes32 swapTarget_CodeHash
    ) {
        factory = IModuleFactory(moduleFactory);
        if (arbiter_ == address(0) && arbiter_.codehash != arbiter_CodeHash) {
            revert InvalidArbiterAddress();
        }
        if (oracle_ == address(0) && oracle_.codehash != oracle_CodeHash) {
            revert InvalidOracleAddress();
        }
        if (swapTarget_ == address(0) && swapTarget_.codehash != swapTarget_CodeHash) {
            revert InvalidSwapTargetAddress();
        }
        arbiter = arbiter_;
        oracle = oracle_;
        swapTarget = swapTarget_;
    }
```
## Use of `uint` Instead of `uint256`
Across the codebase, there are instances of `uint`, as opposed to `uint256`. In favor of explicitness, consider replacing all instances of `uint` with `uint256`.

Here is one of the instances entailed:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117
