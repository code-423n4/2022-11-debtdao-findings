
# Gas


| | issue |
| ----------- | ----------- |
| 1 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#1-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 2 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#2-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 3 | [not using the named return variables when a function returns, wastes deployment gas](#3-not-using-the-named-return-variables-when-a-function-returns-wastes-deployment-gas) |
| 4 | [can make the variable outside the loop to save gas](#4-can-make-the-variable-outside-the-loop-to-save-gas) |
| 5 | [`++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops](#5-ii-should-be-uncheckediuncheckedi-when-it-is-not-possible-for-them-to-overflow-as-is-the-case-when-used-in-for-loop-and-while-loops) |
| 6 | [require()/revert() strings longer than 32 bytes cost extra gas](#6-requirerevert-strings-longer-than-32-bytes-cost-extra-gas) |
| 7 | [state variables should be cached in stack variables rather than re-reading them from storage](#7-state-variables-should-be-cached-in-stack-variables-rather-than-re-reading-them-from-storage) |
| 8 | [use a more recent version of solidity](#8-use-a-more-recent-version-of-solidity) |
| 9 | [using `calldata` instead of `memory` for read-only arguments in external functions saves gas](#9-using-calldata-instead-of-memory-for-read-only-arguments-in-external-functions-saves-gas) |
| 10 | [internal functions only called once can be inlined to save gas](#10-internal-functions-only-called-once-can-be-inlined-to-save-gas) |
| 11 | [usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#11-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead) |
| 12 | [public library function should be made private/internal](#12-public-library-function-should-be-made-privateinternal) |


## 1. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

- [SpigotedLine.sol#L122](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L122)

- [SpigotedLine.sol#L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L125)

- [SpigotLib.sol#L96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96)

- [SpigotedLineLib.sol#L101](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L101)
- [SpigotedLineLib.sol#L109](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L109)

- [CreditLib.sol#L183](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L183)


## 2. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [SpigotedLine.sol#L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L125)

- [SpigotedLine.sol#L172](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L172)

- [SpigotedLine.sol#L122](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L122)

- [SpigotedLine.sol#L144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144)

- [EscrowLib.sol#L96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L96)
- [EscrowLib.sol#L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164)
- [EscrowLib.sol#L202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202)


## 3. not using the named return variables when a function returns, wastes deployment gas

- [LineOfCredit.sol#L441](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L441)

- [Spigot.sol#L86](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L86)

- [SpigotLib.sol#L31](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L31)
- [SpigotLib.sol#L85](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L85)
- [SpigotLib.sol#L107](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L107)

- [CreditLib.sol#L80](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L80)
- [CreditLib.sol#L133](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L133)


## 4. can make the variable outside the loop to save gas

- [EscrowLib.sol#L58](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-582-11-03/contracts/utils/EscrowLib.sol#L202)
- [EscrowLib.sol#L61](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-612-11-03/contracts/utils/EscrowLib.sol#L202)


## 5. `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

- [LineOfCredit.sol#L179](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179)
- [LineOfCredit.sol#L203](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203)
- [LineOfCredit.sol#L520](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520)

- [CreditLib.sol#L23](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L23)
- [CreditLib.sol#L51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L51)
- [EscrowLib.sol#L57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-572-11-03/contracts/utils/EscrowLib.sol#L202)


## 6. require()/revert() strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

- [InterestRateCredit.sol#L26](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L26)


## 7. state variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. 

`self.owner`
- [SpigotLib.sol#L105-L122](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L105-L122)


## 8. use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value
Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions


## 9. using `calldata` instead of `memory` for read-only arguments in external functions saves gas

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. 

- [Spigot.sol#L123](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L123)

- [SpigotLib.sol#L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125)


## 10. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

`_accrueInterest`
- [InterestRateCredit.sol#L42](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L42)

`_getNonCaller`
- [MutualConsent.sol#L65](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L65)


## 11. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [LineOfCredit.sol#L224](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L224)
- [LineOfCredit.sol#L225](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L225)
- [LineOfCredit.sol#L249](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L249)
- [LineOfCredit.sol#L250](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L250)

- [SpigotedLine.sol#L32](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L32)

- [Spigot.sol#L143](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L143)

- [Escrow.sol#L24](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L24)

- [InterestRateCredit.sol#L76](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L76)
- [InterestRateCredit.sol#L77](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L77)


## 12. public library function should be made private/internal

Changing from public will remove the compiler-introduced checks for msg.value and decrease the contract’s method ID table size

`_claimRevenue`
- [SpigotLib.sol#L29](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L29)

`trade`
- [SpigotedLineLib.sol#L120](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L120)

`calculateValue`
- [CreditLib.sol#L109](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L109)

`accrue`
- [CreditLib.sol#L239](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L239)

`_getLatestCollateralRatio`
- [EscrowLib.sol#L34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L34)

`_getCollateralValue`
- [EscrowLib.sol#L51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L51)


