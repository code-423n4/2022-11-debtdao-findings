# QA Report
## Found NC [5]

### [NC-01] Pragma is not Forced (Floating Pragma)

https://swcregistry.io/docs/SWC-103

#### Findings [4]:

*/contracts/modules/credit/LineOfCredit.sol*
Line(s): [1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L1)
```solidity
1:	pragma solidity ^0.8.9;
```

*/contracts/modules/credit/SpigotedLine.sol*
Line(s): [1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L1)
```solidity
1:	pragma solidity ^0.8.9;
```

*/contracts/modules/credit/SecuredLine.sol*
Line(s): [1](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L1)
```solidity
1:	pragma solidity ^0.8.9;
```

*/contracts/modules/oracle/Oracle.sol*
Line(s): [2](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L2)
```solidity
2:	pragma solidity ^0.8.9;
```


### [NC-02] Require Statements Missing Error Message

Error messages help with troubleshooting, some require statements are missing error messages.

#### Findings [18]:

*/contracts/modules/credit/LineOfCredit.sol*
Line(s): [112](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112), [326](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326)
```solidity
112:	require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
326:	require(amount <= credit.principal + credit.interestAccrued);
```

*/contracts/modules/credit/SpigotedLine.sol*
Line(s): [62](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62), [143](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143), [160](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160), [239](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239)
```solidity
62:	require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
143:	require(amount <= unusedTokens[credit.token]);
160:	require(msg.sender == borrower);
239:	require(msg.sender == arbiter);
```

*/contracts/modules/credit/EscrowedLine.sol*
Line(s): [90](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L90)
```solidity
90:	require(escrow.updateLine(newLine));
```

*/contracts/utils/EscrowLib.sol*
Line(s): [91](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91), [105](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105), [161](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161), [198](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198), [216](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216)
```solidity
91:	require(amount > 0);
105:	require(msg.sender == ILineOfCredit(self.line).arbiter());
161:	require(amount > 0);
198:	require(amount > 0);
216:	require(msg.sender == self.line);
```

*/contracts/utils/SpigotLib.sol*
Line(s): [128](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L128), [130](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L130), [155](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L155), [180](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180), [189](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189), [201](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201)
```solidity
128:	require(revenueContract != address(this));
130:	require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
155:	require(success);
180:	require(newOwner != address(0));
189:	require(newOperator != address(0));
201:	require(newTreasury != address(0));
```


### [NC-03] Underscore Notation Improves Readability

Solidity allows for the use of _ between every 3 digits of large numbers.

#### Findings [2]:

*/contracts/modules/interest-rate/InterestRateCredit.sol*
Line(s): [9](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L9)
```solidity
9:	uint256 constant BASE_DENOMINATOR = 10000;
```

*/contracts/modules/factories/LineFactory.sol*
Line(s): [14](https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L14)
```solidity
14:	uint32 constant defaultMinCRatio = 3000;
```

### [NC-04] Magic Number Used

#### Findings [4]:

A magic number `18` is used. Consider making a variable in `18`'s place.

*/contracts/utils/EscrowLib.sol*
Line(s): [113](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L113), [137](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L137)

```solidity
113:	deposit.assetDecimals = 18;
137:	deposit.assetDecimals = 18;
```

*/contracts/utils/CreditLib.sol*
Line(s): [140](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L140), [145](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L145)

```solidity
140:	decimals = 18;
145:	decimals = !passed ? 18 : abi.decode(result, (uint8));
```

### [NC-05] `1157...9935` Used Instead of `type(uint256).max` / inconsistency

#### Findings [1]:

*/contracts/utils/EscrowLib.sol*
Line(s): [25-26](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L25-L26)

Using `115792089237316195423570985008687907853269984665640564039457584007913129639935` instead of  `type(uint256).max` can lead to mistyping and degrades readability. Note also that this is not consistant with [L27](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L27) of */contracts/utils/SpigotLib.sol*

```solidity
25:	uint256 constant MAX_INT = 
26:		115792089237316195423570985008687907853269984665640564039457584007913129639935;
```