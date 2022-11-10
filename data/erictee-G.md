### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contracts/utils/CreditLib.sol:L69    return keccak256(abi.encode(line, lender, token));

```

### [G-02] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
contracts/mock/SimpleOracle.sol:L26        require(prices[token] != 0, "SimpleOracle: unsupported token");

contracts/mock/SimpleRevenueContract.sol:L15        require(msg.sender == owner, "Revenue: Only owner can claim");

contracts/mock/SimpleRevenueContract.sol:L17            require(revenueToken.transfer(owner, revenueToken.balanceOf(address(this))), "Revenue: bad transfer");

contracts/mock/SimpleRevenueContract.sol:L26            require(revenueToken.transfer(owner, revenueToken.balanceOf(address(this))), "Revenue: bad transfer");

contracts/mock/SimpleRevenueContract.sol:L34        require(msg.sender == owner, "Revenue: Only owner can operate");

contracts/mock/SimpleRevenueContract.sol:L39        require(msg.sender == owner, "Revenue: Only owner can transfer");

```

### [G-03] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/modules/credit/SpigotedLine.sol:L272    receive() external payable {}

```

### [G-04] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/utils/CreditListLib.sol:L23      for(uint256 i; i < len; ++i) {

contracts/utils/CreditListLib.sol:L51        for(uint i = 1; i < len; ++i) {

contracts/utils/EscrowLib.sol:L57        for (uint256 i; i < length; ++i) {

contracts/modules/credit/LineOfCredit.sol:L179        for (uint256 i; i < len; ++i) {

contracts/modules/credit/LineOfCredit.sol:L203        for (uint256 i; i < len; ++i) {

contracts/modules/credit/LineOfCredit.sol:L520        for (uint256 i; i <= lastSpot; ++i) {

```

### [G-05] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
contracts/modules/interest-rate/InterestRateCredit.sol:L26        require(

```