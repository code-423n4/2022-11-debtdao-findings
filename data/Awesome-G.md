## Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible, some gas can be saved by using an unchecked block.
For example:

[Line 203-207](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203-L207)

```
        for (uint256 i = 0; i < selectorsLength; ++i) {
            bytes4 selector = _selectors[i];
            SelectorToFacet memory oldFacet = ds.selectorToFacet[selector];
            require(oldFacet.facetAddress != address(0), "a2"); // Can't delete a non-existent facet


            _removeOneFunction(oldFacet.facetAddress, selector);
        }
```
##  Using `Storage` Instead of `Memory` for Structs/Arrays Saves Gas
When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declaring the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

There are 3 instances of this issue:
[Line 100](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L100), [Line 139](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L139), [Line 273](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L273)

## Function Order Affects Gas Consumption

public/external function names and public member variable names can be optimized to save gas. See [this](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, per sorted position shifted

## Remove Shorthand Addition/Subtraction Assignment

Instead of using the shorthand of addition/subtraction assignment operators (`+=`, `-=`)
it costs less to remove the shorthand (` x += y ` same as ` x = x+y `) saves ~22 gas
There are 3 instances of this issue:

[Line 276](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L276)

```
Line 276:    credit.deposit += amount;
```

Could be refactored as

```
Line 276:    credit.deposit = credit.deposit + amount;
```

[Line 144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144)

```
Line 144:    unusedTokens[credit.token] -= amount;
```

Could be refactored as

```
Line 144:    unusedTokens[credit.token] = unusedTokens[credit.token] - amount;
```
