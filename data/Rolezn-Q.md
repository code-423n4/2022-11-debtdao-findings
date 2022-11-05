## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Missing Checks for Address(0x0)  | 1 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Unused `receive()` Function Will Lock Ether In Contract  | 1 |
| [LOW&#x2011;3](#LOW&#x2011;3) | The Contract Should `approve(0)` First | 1 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Missing Contract-existence Checks Before Low-level Calls | 11 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Missing parameter validation in `constructor` | 8 |
| [LOW&#x2011;6](#LOW&#x2011;6) | The `nonReentrant` modifier should occur before all other modifiers | 3 |
| [LOW&#x2011;7](#LOW&#x2011;7) | Contracts are not using their OZ Upgradeable counterparts | 30 |

Total: 55 contexts over 7 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Use a more recent version of Solidity | All in-scope contracts |
| [NC&#x2011;2](#NC&#x2011;2) | Redundant Cast | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | `require()` / `revert()` Statements Should Have Descriptive Reason Strings | 18 |
| [NC&#x2011;4](#NC&#x2011;4) | Implementation contract may not be initialized | 7 |
| [NC&#x2011;5](#NC&#x2011;5) | Avoid Floating Pragmas: The Version Should Be Locked | 5 |
| [NC&#x2011;6](#NC&#x2011;6) | Non-usage of specific imports | 1 |
| [NC&#x2011;7](#NC&#x2011;7) | Lines are too long | 14 |

Total: 64 contexts over 7 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Missing Checks for Address(0x0) 

Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

#### <ins>Proof Of Concept</ins>


```
function updateLine: address _line
217: self.line = _line;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L215-L217



#### <ins>Recommended Mitigation Steps</ins>

Consider adding explicit zero-address validation on input parameters of address type.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Unused `receive()` Function Will Lock Ether In Contract 

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

#### <ins>Proof Of Concept</ins>


```
272: receive() external payable {}
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272



#### <ins>Recommended Mitigation Steps</ins>

The function should call another function, otherwise it should revert



### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> The Contract Should `approve(0)` First

Some tokens (like USDT L199) do not work when changing the allowance from an existing non-zero allowance value. They must first be approved by zero and then the actual allowance must be approved.

#### <ins>Proof Of Concept</ins>


```
134: IERC20(sellToken).approve(swapTarget, amount);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L134



#### <ins>Recommended Mitigation Steps</ins>

Approve with a zero amount first before setting the actual amount.




### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Missing Contract-existence Checks Before Low-level Calls

Low-level calls return success if there is no code present at the specified address. 

#### <ins>Proof Of Concept</ins>


```
142: (bool passed, bytes memory result) = token.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L142

```
65: (bool success, bytes memory assetAmount) = token.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L65

```
115: (bool passed, bytes memory tokenAddrBytes) = token.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L115

```
133: .call(abi.encodeWithSignature("decimals()"));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L133

```
58: (bool success, bytes memory returnVal) = spigot.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L58

```
62: (bool success2, bytes memory returnVal2) = escrow.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L62

```
131: (bool success, ) = swapTarget.call{value: amount}(zeroExTradeData);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L131

```
135: (bool success, ) = swapTarget.call(zeroExTradeData);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L135

```
43: (bool claimSuccess,) = revenueContract.call(data);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L43

```
76: (bool success,) = revenueContract.call(data);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L76

```
149: (bool success,) = revenueContract.call(
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L149




#### <ins>Recommended Mitigation Steps</ins>

In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`



### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```
17: escrow = IEscrow(_escrow);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L17

```
55: oracle = IOracle(oracle_);
56: arbiter = arbiter_;
57: borrower = borrower_;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L55-L57

```
14: address oracle_
15: address arbiter_
16: address borrower_
18: address spigot_
19: address escrow_
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L14-L19

```
54: address oracle_
55: address arbiter_
56: address borrower_
57: address spigot_
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L54-L57

```
48: minimumCollateralRatio = _minimumCollateralRatio;
49: oracle = _oracle;
50: borrower = _borrower;
51: state.line = _line;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L48-L51

```
26: factory = IModuleFactory(moduleFactory);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L26

```
16: registry = FeedRegistryInterface(_registry);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L16

```
36: state.owner = _owner;
37: state.operator = _operator;
38: state.treasury = _treasury;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L36-L38



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```
93: function claimAndRepay(address claimToken, bytes calldata zeroExTradeData)
        external
        whileBorrowing
        nonReentrant
        returns (uint256)
    {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L93

```
154: function claimAndTrade(address claimToken, bytes calldata zeroExTradeData)
        external
        whileBorrowing
        nonReentrant
        returns (uint256)
    {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L154

```
85: function claimEscrow(address token)
        external
        nonReentrant
        returns (uint256 claimed) 
    {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L85



#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

According to remappings.txt, the standard openzeppelin contracts are used:
```
openzeppelin/=lib/openzeppelin-contracts/contracts/
```

```
4: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
5: import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L4-L5

```
2: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
4: import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L2-L4

```
12: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
13: import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L12-L13

```
4: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
5: import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L4-L5

```
3: import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L3

```
4: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
5: import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L4-L5

```
5: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
6: import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L5-L6

```
6: import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L6

```
3: import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L3


## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Use a more recent version of Solidity

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)

Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

#### <ins>Proof Of Concept</ins>

`pragma solidity 0.8.9;` was found to be in use in all in-scope contracts.


#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Redundant Cast

The type of the variable is the same as the type to which the variable is being cast

#### <ins>Proof Of Concept</ins>


No need to cast `price` since if its negative then it will be return 0

```
117: return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117

```
60: address(line)
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L60




### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> `require()` / `revert()` Statements Should Have Descriptive Reason Strings

#### <ins>Proof Of Concept</ins>


```
90: require(escrow.updateLine(newLine));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L90

```
112: require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112

```
326: require(amount <= credit.principal + credit.interestAccrued);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326

```
143: require(amount <= unusedTokens[credit.token]);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143

```
160: require(msg.sender == borrower);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160

```
239: require(msg.sender == arbiter);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239

```
91: require(amount > 0);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91

```
105: require(msg.sender == ILineOfCredit(self.line).arbiter());
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105

```
161: require(amount > 0);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161

```
198: require(amount > 0);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198

```
216: require(msg.sender == self.line);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216

```
147: require(ISpigot(spigot).updateOwner(newLine));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L147

```
128: require(revenueContract != address(this));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L128

```
130: require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L130

```
155: require(success);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L155

```
180: require(newOwner != address(0));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180

```
189: require(newOperator != address(0));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189

```
201: require(newTreasury != address(0));
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201






### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```
    constructor(
        address oracle_,
        address arbiter_,
        address borrower_,
        uint256 ttl_
    ) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L49-L53

```
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
        oracle_,
        arbiter_,
        borrower_,
        spigot_,
        swapTarget_,
        ttl_,
        defaultSplit_
    ) EscrowedLine(escrow_) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L14-L30

```
    constructor(
        address oracle_,
        address arbiter_,
        address borrower_,
        address spigot_,
        address payable swapTarget_,
        uint256 ttl_,
        uint8 defaultRevenueSplit_
    ) LineOfCredit(oracle_, arbiter_, borrower_, ttl_) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L53-L61

```
    constructor(
        uint32 _minimumCollateralRatio,
        address _oracle,
        address _line,
        address _borrower
    ) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L42-L47

```
    constructor(
        address moduleFactory,
        address arbiter_,
        address oracle_,
        address swapTarget_
    ) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L20-L25

```
    constructor() {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L19

```
    constructor(address _registry) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L15





### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> Avoid Floating Pragmas: The Version Should Be Locked

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### <ins>Proof Of Concept</ins>

Found usage of floating pragmas ^0.8.9 of Solidity in the following contracts:

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L1

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L1

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L1

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L1

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L2




### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

#### <ins>Proof Of Concept</ins>


```
6: import "../../interfaces/IOracle.sol";
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L6




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length
Reference: https://docs.soliditylang.org/en/v0.8.10/style-guide.html#maximum-line-length

#### <ins>Proof Of Concept</ins>

```
35: // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L35

```
58: // we dont check borrower is same on both lines because borrower might want new address managing new line
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L58

```
28: /// @notice exchange aggregator (mainly 0x router) to trade revenue tokens from a Spigot for credit tokens owed to lenders
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L28

```
23: // the minimum value of the collateral in relation to the outstanding debt e.g. 10% of outstanding debt
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L23

```
8: // Must divide by 100 too offset bps in numerator and divide by another 100 to offset % and get actual token amount
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L8

```
144: // Changes the revenue split between the Treasury and the Owner based upon the status of the Line of Credit
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L144

```
23: /// @notice Emits data re Lender removes funds (principal) - there is no corresponding function, just withdraw()
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L23

```
26: /// @notice Emits data re Lender withdraws interest - there is no corresponding function, just withdraw()
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L26

```
31: /// @notice After accrueInterest runs, emits the amount of interest added to a Borrower's outstanding balance of interest due
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L31

```
1: // forked from https://github.com/IndexCoop/index-coop-smart-contracts/blob/master/contracts/lib/MutualConsent.sol
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L1

```
175: // if Line of Credit is healthy then set the split to the prior agreed default split of revenue tokens
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L175

```
15: // Configurations for revenue contracts related to the split of revenue, access control to claiming revenue tokens and transfer of Spigot ownership
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L15

```
24: // Maximum numerator for Setting.ownerSplit param to ensure that the Owner can't claim more than 100% of revenue
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L24

```
69: // cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L69


