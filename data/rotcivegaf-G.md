# [rotcivegaf] Gas report

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 6 |
| [G&#x2011;02] | Unused lines | 2 |
| [G&#x2011;03] | Declare variable in the return | 1 |
| [G&#x2011;04] | Make view function | 1 |
| [G&#x2011;05] | Unused events | 4 |
| [G&#x2011;06] | Not multiply by one | 1 |


## [G-01] `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```solidity
File: contracts/modules/credit/LineOfCredit.sol

179        for (uint256 i; i < len; ++i) {

203        for (uint256 i; i < len; ++i) {

520        for (uint256 i; i <= lastSpot; ++i) {
```

```solidity
File: contracts/utils/CreditListLib.sol

23      for(uint256 i; i < len; ++i) {

51        for(uint i = 1; i < len; ++i) {
```

```solidity
File: contracts/utils/CreditLib.sol

57        for (uint256 i; i < length; ++i) {
```

## [G-02] Unused lines

This checks could be do offchain:

```solidity
File: contracts/utils/CreditLib.sol

135      int price = IOracle(oracle).getLatestAnswer(token);
136      if(price <= 0 ) { revert NoTokenPrice(); }
```

```solidity
File: contracts/utils/CreditLib.sol

126                int256 price = IOracle(oracle).getLatestAnswer(deposit.asset);
127                if (price <= 0) {
128                    revert InvalidCollateral();
129                }
```

## [G-03] Declare variable in the return

```solidity
File: contracts/modules/credit/LineOfCredit.sol

/// @audit: declare this variable in the returns, L235
239        bytes32 id = _createCredit(lender, token, amount);
```

## [G-04] Make view function

```solidity
File: modules/oracle/Oracle.sol

23    function getLatestAnswer(address token) external returns (int) {
```

## [G-05] Unused events

```solidity
File: contracts/utils/LineFactoryLib.sol

16    event DeployedSpigot(
17        address indexed deployedAt,
18        address indexed owner,
19        address indexed treasury,
20        address operator
21    );

23    event DeployedEscrow(
24        address indexed deployedAt,
25        uint32 indexed minCRatio,
26        address indexed oracle,
27        address owner
28    );
```

```solidity
File: contracts/utils/EscrowLib.sol

227    event Liquidate(address indexed token, uint256 indexed amount);
```

```solidity
File: contracts/utils/CreditLib.sol

38  event Borrow(bytes32 indexed id, uint256 indexed amount);
```

## [G-06] Not multiply by one

```solidity
File: contracts/utils/CreditLib.sol

117    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```
