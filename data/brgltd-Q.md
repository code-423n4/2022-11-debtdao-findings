# [01] Replace .transfer with .call to send ether.

## Impact

.transfer will forward 2300 gas and if the recipient has a fallback/receive function with custom logic, the transation may run of of gas, breaking the contract desired functionality and causing unexpected behavior.

## Proof of Concept

Usage of .transfer to send ether in the library `LineLib`, called by `LineOfCredit` contract.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L48

## Recommended Mitigation Steps

Replace .transfer with .call to prevent potential issues with .transfer. Note that the return of .call needs to be checked.

# [02] Unbounded loop in `LineOfCredit._updateOutstandingDebt()`

## Proof of Concept

`_updateOustandingDebt()` will interate over all `ids`. The `ids` state variable is unbounded.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L172

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L449

## Impact

Updating the debt related functionalities can run out of gas causing a DOS condition.

## Recommended Mitigation Steps

Limit the maximum number of items in `ids`. Alternatively, enable a functionality to update the debt on a chunk of the `ids`, instead of the entire array.

# [03] Locked msg.value

## Impact

For functions able to transfer ether and erc20, if ether is sent accidentaly with token denomiation different than ETH, the msg.value will be locked.

## Proof of Concept

Functions handling both ether and erc20 transfers.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L34-L51

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L59-L74

## Recommended Mitigation Steps

When the token denomination is not ETH, add a check if msg.value is zero, e.g. `require(msg.value == 0)`, to prevent accidentaly locking ether on these transactions.

# [04] `LineOfCredit.borrow()` reentrancy vulnerability

## Impact

Updating the state after external calls that handle control to external contracts allow for attackers to reenter into the original function and break the function state that is updated only after external calls.

## Proof of Concept

`LineOfCredit.borrow()` will update the state variables `ids` and `credits` after the external call to send out ETH or ERC20 tokens. An attacker can callback into `LineOfCreditBorrow()` from a fallback/receive function, or from a malicious token contract.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L360

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L364

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L516-L538

## Recommended Mitigation Steps

Call `_sortIntoQ()` after `LineLib.sendOutTokenOrETH()`. Alternatively, add a `nonReentrant` modifier into `LineOfCredit.borrow()`.

# [05] `LineLib.receiveTokenOrETH()` won't transfer ether

## Proof of Concept

`LineLib.receiveTokenOrETH` won't implement any transfer and will return true if msg.value is bigger than `amount` and the denomination is `ETH`.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L59-L74

## Impact

Impacted is any functionality calling `LineLib` to transfer ether into the contract. It will result in a silent failure.

## Recommended Mitigation Steps

Add the missing functionality to transfer ether into the contract.

# [06] Arbitrary sender in `LineLib.receiveTokenOrETH()` for erc20 transfers

Functions calling into `LineLib.receiveTokenOrETH()` (`LineOfCredit.addCredit() ` and `LineOfCredit.increaseCredit()`) can pass arbitrary addresses into the erc20 transfer function.

## Recommendation

Wherever possible, consider using `msg.sender` as the sender argument to avoid having issues related to undesired addresses receiving credit.

# [07] Use the latest version of solitity

The following contracts are using 0.8.9.

contracts/modules/credit/EscrowedLine.sol,
contracts/modules/spigot/Spigot.sol,
contracts/modules/escrow/Escrow.sol,
contracts/modules/factories/LineFactory.sol,
contracts/utils/CreditLib.sol,
contracts/utils/LineLib.sol,
contracts/utils/CreditListLib.sol,
contracts/utils/EscrowLib.sol,
contracts/utils/SpigotLib.sol,
contracts/utils/SpigotedLineLib.sol,
contracts/utils/MutualConsent.sol,
contracts/utils/LineFactoryLib.sol

Consider using the latest version of solidity to ensure the compiler contains the latest security fixes and improved features. e.g. use version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

# [08] Floating pragma

The following contracts are floating the pragma version.

contracts/modules/credit/LineOfCredit.sol,
contracts/modules/credit/SecuredLine.sol,
contracts/modules/credit/SpigotedLine.sol,
contracts/modules/interest-rate/InterestRateCredit.sol,

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.

Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

# [09] Missing address(0) checks on constructor

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L31-L39

# [10] Critical changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L181-L183

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L196-L205

# [11] Inconsistent usage of curly braces on single-line conditionals

Some one-liner conditionals included curly braces and others do not. Consider normalizing only one approach.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L51

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L55

# [12] Replace memory with calldat for read-only function arguments

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125

# [13] Missing natspec/documentation

Consider adding natspec on all functions to improve documentation.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L120

# [14] Returning a manual value and using the return named varaible feature on the same function

Consider using only one approach, e.g. either returning a manual value, or not returning a manual value and returning the named variable from the function header.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L70

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L85

# [15] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following order for functions:

constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private

The instance bellow shows internal above external.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L88

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L93

# [16] Add a limit for the maximum number of characters per line

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) recommends a maximum of 120 characters.

Consider adding a limit of 120 characters or less to prevent large lines.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L69

# [17] Interchangeable usage of uint and uint256

Consider using only one approach to declare 256 bits integers.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L20

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L81

# [18] Open Todos

Todos should be resolved before deployment.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145

# [19] Constant should be uppercase

Consider refactoring defaultRevenueSplit into DEFAULT_REVENUE_SPLIT

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12
