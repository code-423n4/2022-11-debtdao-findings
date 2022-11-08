# 2022-11-DEBTDAO

## Gas Optimizations Report

### Using `!= 0` on `uints` costs less gas than `> 0`.

This change saves 3 gas per instance/loop

_There are **8** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

398:  if(facilityFee > 0) {

484:  if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }

526:             credits[id].principal > 0
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/utils/EscrowLib.sol

91:    require(amount > 0);

119:          bool is4626 = tokenAddrBytes.length > 0 && passed;

134:          if (decimalBytes.length > 0 && successDecimals) {

161:  require(amount > 0);

198:  require(amount > 0);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied

Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **2** instances of this issue:_

```solidity
File: contracts/utils/CreditLib.sol

188:    credit.interestAccrued = 0;

219:    credit.interestRepaid = 0;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

### Functions that are access-restricted from most users may be marked as `payable`

Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **2** instances of this issue:_

```solidity
File: contracts/utils/EscrowLib.sol

104:  function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {

215:  function updateLine(EscrowState storage self, address _line) external returns(bool) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops

When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **6** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

179:  for (uint256 i; i < len; ++i) {

203:  for (uint256 i; i < len; ++i) {

520:  for (uint256 i; i <= lastSpot; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/utils/CreditListLib.sol

23:   for(uint256 i; i < len; ++i) {

51:    for(uint i = 1; i < len; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol

```solidity
File: contracts/utils/EscrowLib.sol

57:    for (uint256 i; i < length; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

### `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables

_There are **4** instances of this issue:_

```solidity
File: contracts/modules/credit/SpigotedLine.sol

122:      unusedTokens[credit.token] -= repaid - newTokens;

125:      unusedTokens[credit.token] += newTokens - repaid;

144:  unusedTokens[credit.token] -= amount;

172:  unusedTokens[targetToken] += newTokens;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead

'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **29** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

224:  uint128 drate,

225:  uint128 frate,

249:  uint128 drate,

250:  uint128 frate
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/modules/credit/SecuredLine.sol

21:    uint8 defaultSplit_
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol

```solidity
File: contracts/modules/credit/SpigotedLine.sol

32:   uint8 public immutable defaultRevenueSplit;

60:    uint8 defaultRevenueSplit_
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```solidity
File: contracts/modules/escrow/Escrow.sol

24:   uint32 public immutable minimumCollateralRatio;

43:    uint32 _minimumCollateralRatio,
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol

```solidity
File: contracts/modules/factories/LineFactory.sol

12:   uint8 constant defaultRevenueSplit = 90;

13:   uint8 constant MAX_SPLIT = 100;

14:   uint32 constant defaultMinCRatio = 3000;

43:    uint32 minCRatio,

71:     uint8 split = defaultRevenueSplit;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

76:    uint128 dRate,

77:    uint128 fRate
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol

```solidity
File: contracts/modules/spigot/Spigot.sol

146:  function updateOwnerSplit(address revenueContract, uint8 ownerSplit)
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol

```solidity
File: contracts/utils/CreditLib.sol

112:  uint8 decimals

138:  uint8 decimals;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```solidity
File: contracts/utils/LineFactoryLib.sol

13:    uint8 revenueSplit

25:    uint32 indexed minCRatio,

51:    uint8 split = SecuredLine(oldLine).defaultRevenueSplit();

85:    uint8 revenueSplit
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol

```solidity
File: contracts/utils/SpigotLib.sol

25:   uint8 constant MAX_SPLIT = 100;

164:  function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)

252:  uint8 indexed split
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```solidity
File: contracts/utils/SpigotedLineLib.sol

13:   uint8 constant MAX_SPLIT = 100;

169:  function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

170:  (uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol

### Use `calldata` instead of `memory` for function parameters

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **9** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

      /// @audit Store `credit` in calldata.
218:  function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {

465:  function _repay(Credit memory credit, bytes32 id, uint256 amount)

483:  function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/modules/spigot/Spigot.sol

125:  function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol

```solidity
File: contracts/utils/CreditLib.sol

74:   ILineOfCredit.Credit memory credit,

169:  ILineOfCredit.Credit memory credit,

203:  ILineOfCredit.Credit memory credit,

240:  ILineOfCredit.Credit memory credit,
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```solidity
File: contracts/utils/SpigotLib.sol

125:  function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`

In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **11** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

112:  require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

132:  if (block.timestamp >= deadline && count > 0) {

326:  require(amount <= credit.principal + credit.interestAccrued);

520:  for (uint256 i; i <= lastSpot; ++i) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/modules/credit/SpigotedLine.sol

62:    require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

143:  require(amount <= unusedTokens[credit.token]);
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```solidity
File: contracts/utils/CreditLib.sol

117:  return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);

136:  if(price <= 0 ) { revert NoTokenPrice(); }

176:  if (amount <= credit.interestAccrued) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol

```solidity
File: contracts/utils/CreditListLib.sol

42:   if(len <= 1) return true;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol

```solidity
File: contracts/utils/EscrowLib.sol

127:          if (price <= 0) {
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

### Use `immutable` & `constant` for state variables that do not change their value

_There are **2** instances of this issue:_

```solidity
File: contracts/modules/credit/LineOfCredit.sol

38:   LineLib.STATUS public status;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```solidity
File: contracts/modules/oracle/Oracle.sol

14:   FeedRegistryInterface internal registry;
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas

Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **1** instances of this issue:_

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

26:    require(
27:        msg.sender == lineContract,
28:        "InterestRateCred: only line contract."
29:    );
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **1** instances of this issue:_

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

26:    require(
27:        msg.sender == lineContract,
28:        "InterestRateCred: only line contract."
29:    );
```

https://github.com/debtdao/Line-of-Credit/tree/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol
