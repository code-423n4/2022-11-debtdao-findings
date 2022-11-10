# GAS
## use of custom errors rather than revert() / require() error message
### Summary
Custom errors reduce 38 gas if the condition is met and 22 gas otherwise.
Also reduces contract size and deployment costs.
### Details
Since version 0.8.4 the use of custom errors rather than revert() / require() saves gas as noticed in
https://blog.soliditylang.org/2021/04/21/custom-errors/
https://github.com/code-423n4/2022-04-pooltogether-findings/issues/13

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29
### Mitigation
replace each error message in a require by a custom error

## Reduce the size of error messages (Long revert Strings)
### Summary
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29

### Mitigation
Consider shortening the revert strings to fit in 32 bytes



## increments can be unchecked in loops
### Summary
Unchecked operations as the ++i on for loops are cheaper than checked one.

### Details
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline..

    The code would go from:
```
    for (uint256 i; i < numIterations; i++) {
    // ...
    }
```

    to
```
    for (uint256 i; i < numIterations;) {
      // ...
      unchecked { ++i; }
    }
    The risk of overflow is inexistent for a uint256 here.
```

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L23
      for(uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditListLib.sol#L51
        for(uint i = 1; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L179
        for (uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L203
        for (uint256 i; i < len; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L520
        for (uint256 i; i <= lastSpot; ++i) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L57
        for (uint256 i; i < length; ++i) {

### Mitigation
Add unchecked `++i` at the end of all the for loop where it's not expected to overflow and remove them from the for header





## usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead
### Summary
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

### Details
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html 
Use a larger size than downcast where needed

### Github Permalinks


https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L32
    uint8 public immutable defaultRevenueSplit;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L32
    uint8 public immutable defaultRevenueSplit;



### Mitigation
Consider using some data type that uses 32 bytes, for example uint256


## Store using Struct over multiple mappings
### Summary
All these variables could be combine in a Struct in order to reduce the gas cost. 
### Details
As noticed in: 
https://gist.github.com/alexon1234/b101e3ac51bea3cbd9cf06f80eaa5bc2
When multiple mappings that access the same addresses, uints, etc, all of them can be mixed into an struct and then that data accessed like:
mapping(datatype => newStructCreated) newStructMap;
Also, you have this post where it explains the benefits of using Structs over mappings 
https://medium.com/@novablitz/storing-structs-is-costing-you-gas-774da988895e

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L16
    mapping(address => bool) enabled;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L18
    mapping(address => IEscrow.Deposit) deposited;
- - - - -
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L12
    mapping(address => uint256) escrowed; // token  -> amount escrowed

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L16
    mapping(address => ISpigot.Setting) settings; // revenue contract -> settings

### Mitigation
Consider mixing different mappings into an struct when able in order to save gas.









## `>=` cheaper than `>`
### Summary
Strict inequalities ( `>` ) are more expensive than non-strict ones ( `>=` ). This is due to some supplementary checks (`ISZERO`, 3 gas)

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L132
        if (block.timestamp >= deadline && count > 0) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L398
        if(facilityFee > 0) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L484
        if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }


https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L487
        if (credit.deposit + credit.interestRepaid > 0) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L119
                bool is4626 = tokenAddrBytes.length > 0 && passed;

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L134
                if (decimalBytes.length > 0 && successDecimals) {

### Mitigation
Consider using `>= 1` instead of `> 0` to avoid some opcodes





## `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for state variables
### Summary
`x+=y` costs more gas than x=x+y for state variables. Same happens with -=
### Github Permalinks
- `+=`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L125
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L172
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L96

- `-=`
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L122
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L144
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L164
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L202

### Mitigation
Don't use `+=` for state variables as it cost more gas.


## `abi.encode()` is less gas efficient than `abi.encodePacked()`
### Summary
In general, `abi.encodePacked` is more gas-efficient.

### Details
Changing the abi.encode function to `abi.encodePacked` can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, `abi.encodePacked` is more gas-efficient.
### Github Permalinks


https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/CreditLib.sol#L69
    return keccak256(abi.encode(line, lender, token));


### Mitigation
Consider changing abi.encode to `abi.encodePacked`


## Internal functions only called once can be inlined to save gas
### Summary
Not inlining costs `20` to `40` gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Github Permalinks

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L516
    function _sortIntoQ(bytes32 p) internal returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/MutualConsent.sol#L65
    function _getNonCaller(address _signerOne, address _signerTwo) internal view returns(address) {



### Mitigation
Consider changing internal function only called once to inline code for gas savings

## Unnecesary storage read
### Summary
A value which value is already known can be used directly rather than reading it from the storage
### Example
```
        line = LineFactoryLib.deploySecuredLine(
            oracle,
            arbiter,
            borrower,
            payable(swapTarget),
            s,
            e,
            ttl,
            split
        );
        // give modules from address(this) to line so we can run line.init()
        LineFactoryLib.transferModulesToLine(address(line), s, e);
        emit DeployedSecuredLine(address(line), s, e, swapTarget, split);
```

The second parameter of SetPricePerShare can use now directly to avoid unnecessary storage read of lastPricePerShareUpdate to save some gas.

Recommendation
Change to:

```
        address _swapTarget = swapTarget;
        line = LineFactoryLib.deploySecuredLine(
            oracle,
            arbiter,
            borrower,
            payable(_swapTarget),
            s,
            e,
            ttl,
            split
        );
        // give modules from address(this) to line so we can run line.init()
        LineFactoryLib.transferModulesToLine(address(line), s, e);
        emit DeployedSecuredLine(address(line), s, e, _swapTarget, split);
```

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L76-L85

### Github Permalinks
https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L76-L85

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L107-L125

https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/factories/LineFactory.sol#L150-L167



### Mitigation
Set directly the value to avoid unnecessary storage read to save some gas

