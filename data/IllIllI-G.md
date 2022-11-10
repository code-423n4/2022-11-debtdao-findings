
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | State variables only set in the constructor should be declared `immutable` | 2 | 4194 |
| [G&#x2011;02] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 5 | 600 |
| [G&#x2011;03] | Using `storage` instead of `memory` for structs/arrays saves gas | 3 | 12600 |
| [G&#x2011;04] | Avoid contract existence checks by using low level calls | 27 | 2700 |
| [G&#x2011;05] | State variables should be cached in stack variables rather than re-reading them from storage | 5 | 485 |
| [G&#x2011;06] | `internal` functions only called once can be inlined to save gas | 4 | 80 |
| [G&#x2011;07] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 3 | 255 |
| [G&#x2011;08] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 6 | 360 |
| [G&#x2011;09] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 1 | - |
| [G&#x2011;10] | Optimize names to save gas | 15 | 330 |
| [G&#x2011;11] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 2 | - |
| [G&#x2011;12] | Using `private` rather than `public` for constants, saves gas | 3 | - |
| [G&#x2011;13] | Inverting the condition of an `if`-`else`-statement wastes gas | 2 | - |
| [G&#x2011;14] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 1 | - |
| [G&#x2011;15] | Use custom errors rather than `revert()`/`require()` strings to save gas | 1 | - |
| [G&#x2011;16] | Functions guaranteed to revert when called by normal users can be marked `payable` | 4 | 84 |

Total: 84 instances over 16 issues with **21688 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.


## Gas Optimizations

### [G&#x2011;01]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**). 

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

*There are 2 instances of this issue:*
```solidity
File: contracts/modules/oracle/Oracle.sol

/// @audit registry (constructor)
16:           registry = FeedRegistryInterface(_registry);

/// @audit registry (access)
29:           ) = registry.latestRoundData(token, Denominations.USD);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L16

### [G&#x2011;02]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/spigot/Spigot.sol

/// @audit setting
125:      function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L125

```solidity
File: contracts/utils/CreditLib.sol

/// @audit credit
73      function getOutstandingDebt(
74        ILineOfCredit.Credit memory credit,
75        bytes32 id,
76        address oracle,
77        address interestRate
78      )
79        external
80:       returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)

/// @audit credit
168     function repay(
169       ILineOfCredit.Credit memory credit,
170       bytes32 id,
171       uint256 amount
172     )
173       external
174:      returns (ILineOfCredit.Credit memory)

/// @audit credit
202     function withdraw(
203       ILineOfCredit.Credit memory credit,
204       bytes32 id,
205       uint256 amount
206     )
207       external
208:      returns (ILineOfCredit.Credit memory)

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L73-L80

```solidity
File: contracts/utils/SpigotLib.sol

/// @audit setting
125:      function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L125

### [G&#x2011;03]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There are 3 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

205:            Credit memory credit = credits[id];

323:          Credit memory credit = credits[id];

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L205

```solidity
File: contracts/modules/credit/SpigotedLine.sol

139:        Credit memory credit = credits[id];

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L139

### [G&#x2011;04]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 27 instances of this issue:*
```solidity
File: contracts/modules/credit/SecuredLine.sol

/// @audit status()
57:       if(ILineOfCredit(newLine).status() != LineLib.STATUS.UNINITIALIZED) { revert BadNewLine(); }

/// @audit init()
63:       if(ILineOfCredit(newLine).init() != LineLib.STATUS.ACTIVE) { revert BadRollover(); }

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L57

```solidity
File: contracts/utils/CreditLib.sol

/// @audit getLatestAnswer()
84:       int256 price = IOracle(oracle).getLatestAnswer(c.token);

/// @audit getLatestAnswer()
135:        int price = IOracle(oracle).getLatestAnswer(token);

/// @audit accrueInterest()
251:        uint256 accruedToken = IInterestRateCredit(interest).accrueInterest(

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L84

```solidity
File: contracts/utils/EscrowLib.sol

/// @audit updateOutstandingDebt()
35:           (uint256 principal, uint256 interest) = ILineOfCredit(self.line).updateOutstandingDebt();

/// @audit arbiter()
105:          require(msg.sender == ILineOfCredit(self.line).arbiter());

/// @audit getLatestAnswer()
126:                  int256 price = IOracle(oracle).getLatestAnswer(deposit.asset);

/// @audit status()
173:            ILineOfCredit(self.line).status() != LineLib.STATUS.REPAID // if repaid, skip;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L35

```solidity
File: contracts/utils/LineFactoryLib.sol

/// @audit spigot()
48:           address s = address(SecuredLine(oldLine).spigot());

/// @audit escrow()
49:           address e = address(SecuredLine(oldLine).escrow());

/// @audit swapTarget()
50:           address payable st = SecuredLine(oldLine).swapTarget();

/// @audit defaultRevenueSplit()
51:           uint8 split = SecuredLine(oldLine).defaultRevenueSplit();

/// @audit init()
72:           if(SecuredLine(payable(line)).init() != LineLib.STATUS.ACTIVE) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L48

```solidity
File: contracts/utils/LineLib.sol

/// @audit safeTransfer()
46:               IERC20(token).safeTransfer(receiver, amount);

/// @audit transfer()
48:               payable(receiver).transfer(amount);

/// @audit safeTransferFrom()
69:               IERC20(token).safeTransferFrom(sender, address(this), amount);

/// @audit balanceOf()
83:               IERC20(token).balanceOf(address(this)) :

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L46

```solidity
File: contracts/utils/SpigotedLineLib.sol

/// @audit claimEscrow()
73:           uint256 claimed = ISpigot(spigot).claimEscrow(claimToken);

/// @audit approve()
134:              IERC20(sellToken).approve(swapTarget, amount);

/// @audit updateOwner()
147:        require(ISpigot(spigot).updateOwner(newLine));

/// @audit owner()
153:        address owner_ = ISpigot(spigot).owner();

/// @audit getSetting()
170:          (uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);

/// @audit updateOwnerSplit()
176:              return ISpigot(spigot).updateOwnerSplit(revenueContract, defaultSplit);

/// @audit updateOwnerSplit()
179:              return ISpigot(spigot).updateOwnerSplit(revenueContract, MAX_SPLIT);

/// @audit updateOwner()
196:            if(!ISpigot(spigot).updateOwner(to)) { revert ReleaseSpigotFailed(); }

/// @audit updateOwner()
201:            if(!ISpigot(spigot).updateOwner(to)) { revert ReleaseSpigotFailed(); }

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L73

### [G&#x2011;05]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 5 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

/// @audit count on line 499
502:          if (count == 0) { _updateStatus(LineLib.STATUS.REPAID); }

/// @audit ids on line 172
180:              id = ids[i];

/// @audit ids on line 201
204:            id = ids[i];

/// @audit ids on line 517
521:              id = ids[i];

/// @audit ids on line 532
532:                  ids[i] = ids[nextQSpot];    // id put into old `p` position

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L502

### [G&#x2011;06]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 4 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

167       function _updateOutstandingDebt()
168           internal
169:          returns (uint256 principal, uint256 interest)

435       function _createCredit(
436           address lender,
437           address token,
438           uint256 amount
439       )
440           internal
441:          returns (bytes32 id)

516:      function _sortIntoQ(bytes32 p) internal returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L167-L169

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

42        function _accrueInterest(
43            bytes32 id,
44            uint256 drawnBalance,
45            uint256 facilityBalance
46:       ) internal returns (uint256) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L42-L46

### [G&#x2011;07]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 3 instances of this issue:*
```solidity
File: contracts/modules/credit/SpigotedLine.sol

/// @audit if-condition on line 120
122:              unusedTokens[credit.token] -= repaid - newTokens;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L122

```solidity
File: contracts/utils/SpigotedLineLib.sol

/// @audit if-condition on line 100
101:            uint256 diff = oldClaimTokens - newClaimTokens;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L101

```solidity
File: contracts/utils/SpigotLib.sol

/// @audit if-condition on line 95
96:               require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L96

### [G&#x2011;08]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 6 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

179:          for (uint256 i; i < len; ++i) {

203:          for (uint256 i; i < len; ++i) {

520:          for (uint256 i; i <= lastSpot; ++i) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L179

```solidity
File: contracts/utils/CreditListLib.sol

23:         for(uint256 i; i < len; ++i) {

51:           for(uint i = 1; i < len; ++i) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L23

```solidity
File: contracts/utils/EscrowLib.sol

57:           for (uint256 i; i < length; ++i) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L57

### [G&#x2011;09]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

26            require(
27                msg.sender == lineContract,
28                "InterestRateCred: only line contract."
29:           );

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29

### [G&#x2011;10]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 15 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

/// @audit init(), healthcheck(), counts(), declareInsolvent()
16:   contract LineOfCredit is ILineOfCredit, MutualConsent {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L16

```solidity
File: contracts/modules/credit/SecuredLine.sol

/// @audit liquidate()
11:   contract SecuredLine is SpigotedLine, EscrowedLine, ISecuredLine {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L11

```solidity
File: contracts/modules/credit/SpigotedLine.sol

/// @audit unused(), claimAndRepay(), useAndRepay(), claimAndTrade(), updateOwnerSplit(), addSpigot(), updateWhitelist(), releaseSpigot(), sweep()
22:   contract SpigotedLine is ISpigotedLine, LineOfCredit, ReentrancyGuard {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L22

```solidity
File: contracts/modules/escrow/Escrow.sol

/// @audit isLiquidatable(), updateLine(), addCollateral(), enableCollateral(), releaseCollateral(), getCollateralRatio(), getCollateralValue(), liquidate()
19:   contract Escrow is IEscrow {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L19

```solidity
File: contracts/modules/factories/LineFactory.sol

/// @audit deployEscrow(), deploySpigot(), deploySecuredLine(), deploySecuredLineWithConfig(), deploySecuredLineWithModules(), rolloverSecuredLine()
9:    contract LineFactory is ILineFactory {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L9

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

/// @audit setRate()
5:    contract InterestRateCredit is IInterestRateCredit {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L5

```solidity
File: contracts/modules/oracle/Oracle.sol

/// @audit getLatestAnswer()
13:   contract Oracle is IOracle {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L13

```solidity
File: contracts/modules/spigot/Spigot.sol

/// @audit operator(), claimRevenue(), claimEscrow(), operate(), addSpigot(), removeSpigot(), updateOwnerSplit(), updateOwner(), updateOperator(), updateTreasury(), updateWhitelistedFunction(), getEscrowed(), isWhitelisted(), getSetting()
16:   contract Spigot is ISpigot, ReentrancyGuard {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L16

```solidity
File: contracts/utils/CreditLib.sol

/// @audit computeId(), getOutstandingDebt(), calculateValue(), create(), repay(), withdraw(), accrue()
14:   library CreditLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L14

```solidity
File: contracts/utils/CreditListLib.sol

/// @audit removePosition(), stepQ()
10:   library CreditListLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L10

```solidity
File: contracts/utils/EscrowLib.sol

/// @audit _getLatestCollateralRatio(), _getCollateralValue(), addCollateral(), enableCollateral(), releaseCollateral(), getCollateralRatio(), getCollateralValue(), liquidate(), isLiquidatable(), updateLine()
21:   library EscrowLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L21

```solidity
File: contracts/utils/LineFactoryLib.sol

/// @audit rolloverSecuredLine(), transferModulesToLine(), deploySecuredLine()
7:    library LineFactoryLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L7

```solidity
File: contracts/utils/LineLib.sol

/// @audit sendOutTokenOrETH(), receiveTokenOrETH(), getBalance()
14:   library LineLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L14

```solidity
File: contracts/utils/SpigotedLineLib.sol

/// @audit claimAndTrade(), trade(), rollover(), canDeclareInsolvent(), updateSplit(), releaseSpigot(), sweep()
10:   library SpigotedLineLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L10

```solidity
File: contracts/utils/SpigotLib.sol

/// @audit _claimRevenue(), operate(), claimRevenue(), claimEscrow(), addSpigot(), removeSpigot(), updateOwnerSplit(), updateOwner(), updateOperator(), updateTreasury(), updateWhitelistedFunction(), getEscrowed(), isWhitelisted(), getSetting()
23:   library SpigotLib {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L23

### [G&#x2011;11]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 2 instances of this issue:*
```solidity
File: contracts/utils/CreditLib.sol

/// @audit uint8 decimals
140:            decimals = 18;

/// @audit uint8 decimals
145:            decimals = !passed ? 18 : abi.decode(result, (uint8));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L140

### [G&#x2011;12]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 3 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

21:       uint256 public immutable deadline;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L21

```solidity
File: contracts/modules/credit/SpigotedLine.sol

32:       uint8 public immutable defaultRevenueSplit;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L32

```solidity
File: contracts/modules/escrow/Escrow.sol

24:       uint32 public immutable minimumCollateralRatio;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L24

### [G&#x2011;13]  Inverting the condition of an `if`-`else`-statement wastes gas
Flipping the `true` and `false` blocks instead saves ***[3 gas](https://gist.github.com/IllIllI000/44da6fbe9d12b9ab21af82f14add56b9)***

*There are 2 instances of this issue:*
```solidity
File: contracts/utils/CreditLib.sol

145:            decimals = !passed ? 18 : abi.decode(result, (uint8));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L145

```solidity
File: contracts/utils/EscrowLib.sol

122                   deposit.asset = !is4626
123                       ? token
124:                      : abi.decode(tokenAddrBytes, (address));

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L122-L124

### [G&#x2011;14]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

/// @audit expensive op on line 324
326:          require(amount <= credit.principal + credit.interestAccrued);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L326

### [G&#x2011;15]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There is 1 instance of this issue:*
```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

26            require(
27                msg.sender == lineContract,
28                "InterestRateCred: only line contract."
29:           );

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29

### [G&#x2011;16]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*
```solidity
File: contracts/modules/credit/LineOfCredit.sol

340       function borrow(bytes32 id, uint256 amount)
341           external
342           override
343           whileActive
344           onlyBorrower
345:          returns (bool)

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L340-L345

```solidity
File: contracts/modules/credit/SecuredLine.sol

48      function rollover(address newLine)
49        external
50        onlyBorrower
51        override
52:       returns(bool)

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L48-L52

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

34        function accrueInterest(
35            bytes32 id,
36            uint256 drawnBalance,
37            uint256 facilityBalance
38:       ) external override onlyLineContract returns (uint256) {

74        function setRate(
75            bytes32 id,
76            uint128 dRate,
77            uint128 fRate
78:       ) external onlyLineContract returns (bool) {

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L34-L38

___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `bool`s for storage incurs overhead | 1 | 17100 |
| [G&#x2011;02] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 3 | 18 |

Total: 4 instances over 2 issues with **17118 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.



## Gas Optimizations

### [G&#x2011;01]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There is 1 instance of this issue:*
```solidity
File: contracts/utils/MutualConsent.sol

/// @audit (valid but excluded finding)
15:       mapping(bytes32 => bool) public mutualConsents;

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L15

### [G&#x2011;02]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There are 3 instances of this issue:*
```solidity
File: contracts/utils/EscrowLib.sol

/// @audit (valid but excluded finding)
91:           require(amount > 0);

/// @audit (valid but excluded finding)
161:          require(amount > 0);

/// @audit (valid but excluded finding)
198:          require(amount > 0);

```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L91

