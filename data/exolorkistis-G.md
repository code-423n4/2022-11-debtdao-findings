[1]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 26 instances of this issue*

```
File:  Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

224:  uint128 drate,

225:  uint128 frate,

249:  uint128 drate,

250:  uint128 frate
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L224-225](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L224-225)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L249-250](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L249-250)


```
File:  Line-of-Credit/contracts/modules/credit/SpigotedLine.sol

32:  uint8 public immutable defaultRevenueSplit;

60:   uint8 defaultRevenueSplit_
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L32](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L32)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L60](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L60)


```
File:  Line-of-Credit/contracts/modules/credit/SecuredLine.sol

21:   uint8 defaultSplit_

```
[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SecuredLine.sol#L21](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SecuredLine.sol#L21)

```
File:  Line-of-Credit/contracts/modules/spigot/Spigot.sol


146:  function updateOwnerSplit(address revenueContract, uint8 ownerSplit)
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/spigot/Spigot.sol#L146](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/spigot/Spigot.sol#L146)


```
File:  Line-of-Credit/contracts/modules/escrow/Escrow.sol

24:  uint32 public immutable minimumCollateralRatio;

43:   uint32 _minimumCollateralRatio,
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L24](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L24)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L43](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L43)


```
File:  Line-of-Credit/contracts/modules/factories/LineFactory.sol

12:  uint8 constant defaultRevenueSplit = 90;

13:  uint8 constant MAX_SPLIT = 100;

14:   uint32 constant defaultMinCRatio = 3000;

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/factories/LineFactory.sol#L12-L14](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/factories/LineFactory.sol#L12-L14)


```
File:  Line-of-Credit/contracts/utils/CreditLib.sol

112:  uint8 decimals

138:   uint8 decimals;
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L112](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L112)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L138](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L138)

```
File:  Line-of-Credit/contracts/utils/SpigotLib.sol

25:  uint8 constant MAX_SPLIT = 100;

164:  function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)

229:     returns(uint8, bytes4, bytes4)

252:    uint8 indexed split

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L25](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L25)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L164](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L164)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L229](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L229)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L252](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L252)

```
File:  Line-of-Credit/contracts/utils/SpigotedLineLib.sol

13: uint8 constant MAX_SPLIT = 100;

169:  function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

170:    (uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L13](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L13)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L169](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L169)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L170](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L170)

```
File:  Line-of-Credit/contracts/utils/LineFactoryLib.sol

13:  uint8 revenueSplit

25:   uint32 indexed minCRatio,

51:  uint8 split = SecuredLine(oldLine).defaultRevenueSplit();

85:   uint8 revenueSplit
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L13](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L13)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L25](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L25)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L51](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L51)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L85](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineFactoryLib.sol#L85)

[2] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 10 instances of this issue:*


```
File: Line-of-Credit/contracts/utils/SpigotedLineLib.sol

13: uint8 constant MAX_SPLIT = 100;

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L13](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L13)

```
File:  Line-of-Credit/contracts/utils/SpigotLib.sol

25:  uint8 constant MAX_SPLIT = 100;

27:   uint256 constant MAX_REVENUE = type(uint256).max / MAX_SPLIT;
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L25](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L25)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L27](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L27)

```
File:  Line-of-Credit/contracts/utils/EscrowLib.sol

25-26:  uint256 constant MAX_INT =
        115792089237316195423570985008687907853269984665640564039457584007913129639935;
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L25-L26](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L25-L26)

```
File:  Line-of-Credit/contracts/modules/factories/LineFactory.sol

12:  uint8 constant defaultRevenueSplit = 90;

13:   uint8 constant MAX_SPLIT = 100;

14:     uint32 constant defaultMinCRatio = 3000;
```
[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/factories/LineFactory.sol#L12-L14](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/factories/LineFactory.sol#L12-L14)

```
File: Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol

7: uint256 constant ONE_YEAR = 365.25 days;

9: uint256 constant BASE_DENOMINATOR = 10000;

11:   uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;

```
[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L7](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L7)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L9](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L9)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L11](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L11)


[3]  FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*

```

File:  Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

340-344:   function borrow(bytes32 id, uint256 amount)
        external
        override
        whileActive
        onlyBorrower

```
[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L340-L344](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L340-344)


```
File: Line-of-Credit/contracts/modules/credit/SecuredLine.sol

48-52:  function rollover(address newLine)
    external
    onlyBorrower
    override
    returns(bool)
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SecuredLine.sol#L48-L52](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SecuredLine.sol#L48-L52)

```
File:  Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol

34-39:  function accrueInterest(
        bytes32 id,
        uint256 drawnBalance,
        uint256 facilityBalance
    ) external override onlyLineContract returns (uint256) {
        return _accrueInterest(id, drawnBalance, facilityBalance);


74-83:  function setRate(
        bytes32 id,
        uint128 dRate,
        uint128 fRate
    ) external onlyLineContract returns (bool) {
        rates[id] = Rate({
            dRate: dRate,
            fRate: fRate,
            lastAccrued: block.timestamp
        });

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L34-L39](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L34-L39)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L83](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L83)


[4] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 5 instances of this issue:*

```

File: Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

179:  for (uint256 i; i < len; ++i) {

203:  for (uint256 i; i < len; ++i) {
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L179](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L179)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L203](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L203)

```
File: Line-of-Credit/contracts/utils/EscrowLib.sol

57:  for (uint256 i; i < length; ++i) {
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L57](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L57)

```
File:  Line-of-Credit/contracts/utils/CreditListLib.sol

23: for(uint256 i; i < len; ++i) {

51:  for(uint i = 1; i < len; ++i) {

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditListLib.sol#L23](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditListLib.sol#L23)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditListLib.sol#L51](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditListLib.sol#L51)

[5]  STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

Avoids a Gsset (20000 gas) in the constructor, and replaces the first access in each transaction (Gcoldsload - 2100 gas) and each access thereafter (Gwarmacces - 100 gas) with a ``PUSH32`` (3 gas).

*There are 13 instances of this issue:*

```
File:  Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

51:  address arbiter_,

52:  address borrower_,

53:  uint256 ttl_
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L51-L53](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L51-L53)

```

File: Line-of-Credit/contracts/modules/credit/SpigotedLine.sol

55:   address arbiter_,

56:  address borrower_,

57:  address spigot_,

58:  address payable swapTarget_,

59:   uint256 ttl_,

60:    uint8 defaultRevenueSplit_

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L55-L60](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/SpigotedLine.sol#L55-L60)


```
File:  Line-of-Credit/contracts/modules/escrow/Escrow.sol

43:  uint32 _minimumCollateralRatio,

44:  address _oracle,

45:  address _line,

46:   address _borrower
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L43-L46](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/escrow/Escrow.sol#L43-L46)


[6]  ``ABI.ENCODE()`` IS LESS EFFICIENT THAN ``ABI.ENCODEPACKED()``

*There is 1 instance of this issue:*

```
File:   Line-of-Credit/contracts/utils/CreditLib.sol

69:   return keccak256(abi.encode(line, lender, token));

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L69](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/CreditLib.sol#L69)


[7]  USE ASSEMBLY TO CHECK FOR ADDRESS(0) to save gas.

*There are 3 instances of this issue:*

```
File:  Line-of-Credit/contracts/utils/SpigotLib.sol


180: require(newOwner != address(0));

189:   require(newOperator != address(0));

201:    require(newTreasury != address(0));
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L180](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L180)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L189](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L189)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L201](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L201)


[8]  DUPLICATED ``REQUIRE()``/``REVERT()`` CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Saves deployment cost

*There are 5 instances of this issue:*

```
File: Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

241: require(interestRate.setRate(id, drate, frate));

259:   require(interestRate.setRate(id, drate, frate));

```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L241](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L241)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L259](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L259)

```
File: Line-of-Credit/contracts/utils/EscrowLib.sol

91:  require(amount > 0);

161:  require(amount > 0);

198:  require(amount > 0);
```

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L91](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L91)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L161](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L161)

[https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L198](https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L198)




