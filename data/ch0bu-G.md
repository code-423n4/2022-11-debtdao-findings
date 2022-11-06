## 1. Use a more recent version of solidity

- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
All contracts use Solidity version 0.8.9
```

## 2. Usage of `uint/int` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.


## 3. Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback()` payable functions that are not used, can be removed to save deployment gas.

```
272	receive() external payable {}
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol


## 4. Constructor parameters should be avoided when possible

Constructor parameters are expensive. The contract deployment will be cheaper in gas if they are hard coded instead of using constructor parameters.

```
55	oracle = IOracle(oracle_);
56	arbiter = arbiter_;
57	borrower = borrower_;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol
```
64	spigot = ISpigot(spigot_);
65	defaultRevenueSplit = defaultRevenueSplit_;
66	swapTarget = swapTarget_;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol
```
48	minimumCollateralRatio = _minimumCollateralRatio;
49	oracle = _oracle;
50	borrower = _borrower;
51	state.line = _line;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol
```
36	arbiter = arbiter_;
37	oracle = oracle_;
38	swapTarget = swapTarget_;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol
