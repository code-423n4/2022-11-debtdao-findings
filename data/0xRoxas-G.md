# Gas Report
## Optimizations found [6]

### [G-01] Public Constants Can Be Made Private to Save Gas

#### Findings [9]:

*/contracts/modules/interest-rate/InterestRateCredit.sol*
Line(s): [7](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L7), [9](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L9), [11](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L11)
```solidity
7:	uint256 constant ONE_YEAR = 365.25 days;
9:	uint256 constant BASE_DENOMINATOR = 10000;
11:	uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;
```

*/contracts/modules/factories/LineFactory.sol*
Line(s): [12](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12), [13](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L13), [14](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L14)
```solidity
12:	uint8 constant defaultRevenueSplit = 90;
13:	uint8 constant MAX_SPLIT = 100;
14:	uint32 constant defaultMinCRatio = 3000;
```

*/contracts/utils/EscrowLib.sol*
Line(s): [25](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L25)
```solidity
25:	uint256 constant MAX_INT =
```

*/contracts/utils/SpigotLib.sol*
Line(s): [25](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L25), [27](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L27)
```solidity
25:	uint8 constant MAX_SPLIT = 100;
27:	uint256 constant MAX_REVENUE = type(uint256).max / MAX_SPLIT;
```


### [G-02] Use nested if instead of && [ⓘ](https://github.com/devanshbatham/Solidity-Gas-Optimization-Tips#6--use-nested-if-and-avoid-multiple-check-combinations)

#### Findings [12]:

*/contracts/modules/credit/LineOfCredit.sol*
Line(s): [132](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132), [391](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L391)
```solidity
132:	if (block.timestamp >= deadline && count > 0) {
391:	if(msg.sender != credit.lender && msg.sender != b) {
```

*/contracts/modules/credit/SpigotedLine.sol*
Line(s): [102](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L102), [140](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L140)
```solidity
102:	if (msg.sender != borrower && msg.sender != credit.lender) {
140:	if (msg.sender != borrower && msg.sender != credit.lender) {
```

*/contracts/utils/EscrowLib.sol*
Line(s): [134](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L134)
```solidity
134:	if (decimalBytes.length > 0 && successDecimals) {
```

*/contracts/utils/SpigotLib.sol*
Line(s): [197](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L197)
```solidity
197:	if(msg.sender != self.operator && msg.sender != self.treasury) {
```

*/contracts/utils/SpigotedLineLib.sol*
Line(s): [174](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L174), [177](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L177), [195](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L195), [200](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L200), [220](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L220)
```solidity
174:	if(status == LineLib.STATUS.ACTIVE && split != defaultSplit) {
177:	} else if (status == LineLib.STATUS.LIQUIDATABLE && split != MAX_SPLIT) {
195:	if (status == LineLib.STATUS.REPAID &&  msg.sender == borrower) {
200:	if (status == LineLib.STATUS.LIQUIDATABLE && msg.sender == arbiter) {
220:	if (status == LineLib.STATUS.REPAID && msg.sender == borrower) {
```

*/contracts/utils/MutualConsent.sol*
Line(s): [39](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L39)
```solidity
39:	if(msg.sender != _signerOne && msg.sender != _signerTwo) { revert Unauthorized(); }
```


### [G-03] Unless Used for Variable Packing uint8 May Be More Expensive Than Using uint256 [ⓘ](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-15-usage-of-uint8-may-increase-gas-cost)

#### Findings [7]:

*/contracts/modules/credit/SpigotedLine.sol*
Line(s): [32](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L32), [60](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L60)
```solidity
32:	uint8 public immutable defaultRevenueSplit;
60:	uint8 defaultRevenueSplit_
```

*/contracts/modules/factories/LineFactory.sol*
Line(s): [12](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L12), [13](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L13), [71](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L71)
```solidity
12:	uint8 constant defaultRevenueSplit = 90;
13:	uint8 constant MAX_SPLIT = 100;
71:	uint8 split = defaultRevenueSplit;
```

*/contracts/utils/SpigotLib.sol*
Line(s): [25](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L25)
```solidity
25:	uint8 constant MAX_SPLIT = 100;
```

*/contracts/utils/SpigotedLineLib.sol*
Line(s): [13](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L13)
```solidity
13:	uint8 constant MAX_SPLIT = 100;
```

### [G-04] Variable Can be Made Immutable to Save Gas 

If a variable is only initialized in the constructor of a function it can be made `immutable` to save gas.

**Example:**

```solidity
60:	IVotingEscrow public votingEscrow;
```

**to**

```solidity
60:	IVotingEscrow public immutable votingEscrow;
```

#### Findings [2]:

*/contracts/WardenPledge.sol*
Line(s): [60](https://github.com/code-423n4/2022-10-paladin/blob/main/contracts/WardenPledge.sol#L60), [62](https://github.com/code-423n4/2022-10-paladin/blob/main/contracts/WardenPledge.sol#L62)
```solidity
60:	IVotingEscrow public votingEscrow;
62:	IBoostV2 public delegationBoost;
```

### [G-05] Using `unchecked` In For Loop With uint256 Iterator

The iterator will not overflow, `unchecking` the iterator saves gas.

*/contracts/modules/credit/LineOfCredit.sol*
Line(s): [179](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179), [203](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203), [520](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520)

```solidity
179:	for (uint256 i; i < len; ++i) {
203:	for (uint256 i; i < len; ++i) {
520:	for (uint256 i; i <= lastSpot; ++i) {
```

*/contracts/utils/EscrowLib.sol*
Line(s): [57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57)

```solidity
57:	for (uint256 i; i < length; ++i) {
```

*/contracts/utils/CreditListLib.sol*
Line(s): [23](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L23), [51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51)

```solidity
23:	for(uint256 i; i < len; ++i) {
51:	for(uint i = 1; i < len; ++i) {
```

### [G-06] Using `unchecked` when Arithmetic Cannot Overflow Saves gas

There are mutliple occurances where arithmetic is performed that cannot overflow / underflow based on a previous require. Adding an `unchecked` brace around these occurances will save gas (Solidity will not force an underflow / overflow).

**NOTE:** Findings show the check before each line that allows the second line to be `unchecked`.  Only the line following the check should be unchecked (NOT the check itself).

```solidity
unchecked {
	//Code
}
```

#### Findings [4]:

*/contracts/utils/EscrowLib.sol*
Line(s): [163-164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L163-164), [200](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L200) / [202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202)

```solidity
163:	if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }
164:	self.deposited[token].amount -= amount;
```

```solidity
200:	if(self.deposited[token].amount < amount) { revert InvalidCollateral(); }
202:	self.deposited[token].amount -= amount;
```


*/contracts/utils/SpigotedLineLib.sol*
Line(s): [100-101](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L100-L101)

```solidity
100:	if(oldClaimTokens > newClaimTokens) {
101:	uint256 diff = oldClaimTokens - newClaimTokens;
```

*/contracts/modules/credit/SpigotedLine.sol*
Line(s): [143-144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143-L144)

```solidity
143:	require(amount <= unusedTokens[credit.token]);
144:	unusedTokens[credit.token] -= amount;
```