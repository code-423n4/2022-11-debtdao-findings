## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|
|-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | Empty Blocks Should Be Removed Or Emit Something | 1 |
| [GAS&#x2011;2](#GAS&#x2011;2) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 6 |
| [GAS&#x2011;3](#GAS&#x2011;3) | `abi.encode()` is less efficient than `abi.encodepacked()` | 1 |
| [GAS&#x2011;4](#GAS&#x2011;4) | Use assembly to check for `address(0)` | 1 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 12 |
| [GAS&#x2011;6](#GAS&#x2011;6) | Optimize names to save gas | 10 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states | 5 |
| [GAS&#x2011;8](#GAS&#x2011;8) | Do not calculate constants | 5 |
| [GAS&#x2011;9](#GAS&#x2011;9) | Structs can be packed into fewer storage slots | 2 |

Total: 80 contexts over 15 issues

## Gas Optimizations



### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```
272: receive() external payable {}
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272




### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```
179: for (uint256 i; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179

```
203: for (uint256 i; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203

```
520: for (uint256 i; i <= lastSpot; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520

```
23: for(uint256 i; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L23

```
51: for(uint i = 1; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51

```
57: for (uint256 i; i < length; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57




### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```
69: return keccak256(abi.encode(line, lender, token));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L69







### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Use assembly to check for `address(0)`

Save 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}
```

#### <ins>Proof Of Concept</ins>


```
81: if(token == address(0)) return 0;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L81




### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```
32: uint8 public immutable defaultRevenueSplit;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L32

```
24: uint32 public immutable minimumCollateralRatio;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L24

```
12: uint8 constant defaultRevenueSplit = 90;
13: uint8 constant MAX_SPLIT = 100;
14: uint32 constant defaultMinCRatio = 3000;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12-L14

```
71: uint8 split = defaultRevenueSplit;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L71

```
138: uint8 decimals;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L138

```
51: uint8 split = SecuredLine(oldLine).defaultRevenueSplit();
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L51

```
13: uint8 constant MAX_SPLIT = 100;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L13

```
25: uint8 constant MAX_SPLIT = 100;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L25






### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Optimize names to save gas

`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://github.com/enzosv/solidity-optimize-name) link for an example of how it works. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

#### <ins>Proof Of Concept</ins>

All in-scope contracts






### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Use `uint256(1)`/`uint256(2)` instead for `true` and `false` boolean states

If you don't use boolean for storage you will avoid Gwarmaccess 100 gas. In addition, state changes of boolean from `true` to `false` can cost up to ~20000 gas rather than `uint256(2)` to `uint256(1)` that would cost significantly less.

#### <ins>Proof Of Concept</ins>


```
16: mapping(address => bool) enabled;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L16

```
15: mapping(bytes32 => bool) public mutualConsents;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L15

```
14: mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L14






### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```
11: uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L11

```
27: uint256 constant MAX_REVENUE = type(uint256).max / MAX_SPLIT;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L27


### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Structs can be packed into fewer storage slots 

Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

#### <ins>Proof Of Concept</ins>

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/interfaces/ILineFactory.sol#L4-L9

```
  struct CoreLineParams {
      address borrower;
      uint256 ttl;
      uint32 cratio;
      uint8 revenueSplit;
  }
```

Can save 2 slots by changing to:

```
  struct CoreLineParams {
      address borrower; //@audit 160 bits
      uint32 cratio; //@audit 32 bits
      uint8 revenueSplit; //@audit 8 bits
      uint256 ttl;
  }
```


https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L7-L16

```
  struct SpigotState {
      address owner;
      address operator;
      address treasury;
      // Total amount of revenue tokens escrowed by the Spigot and available to be claimed
      mapping(address => uint256) escrowed; // token  -> amount escrowed
      // Functions that the operator is allowed to run on all revenue contracts controlled by the Spigot
      mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
      // Configurations for revenue contracts related to the split of revenue, access control to claiming revenue tokens and transfer of Spigot ownership
      mapping(address => ISpigot.Setting) settings; // revenue contract -> settings
  }
```

Can potentially save 1 slot due to mapping of `whitelistedFunctions` sized bool (8 bits) by putting after `address treasury` sized 160 bits:

```
  struct SpigotState {
      address owner;
      address operator;
      address treasury;
      // Functions that the operator is allowed to run on all revenue contracts controlled by the Spigot
      mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
      // Total amount of revenue tokens escrowed by the Spigot and available to be claimed
      mapping(address => uint256) escrowed; // token  -> amount escrowed
      // Configurations for revenue contracts related to the split of revenue, access control to claiming revenue tokens and transfer of Spigot ownership
      mapping(address => ISpigot.Setting) settings; // revenue contract -> settings
  }
```