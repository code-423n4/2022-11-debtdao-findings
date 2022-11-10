# ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

```solidity
File: CreditListLib.sol
23:       for(uint256 i; i < len; ++i) {

51:       for(uint i = 1; i < len; ++i) {
```

# USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html

Each operation involving a uint8 costs an extra 28 gas as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so. Use a larger size then downcast where needed.


```solidity
File: ILineFactory.sol
04:     struct CoreLineParams {
05:         address borrower;
06:         uint256 ttl;
07:         uint32 cratio;
08:         uint8 revenueSplit;
09:     }

File: ISpigot.sol
5:     struct Setting {
6:         uint8 ownerSplit;             // x/100 % to Owner, rest to Treasury
7:         bytes4 claimFunction;         // function signature on contract to call and claim revenue
8:         bytes4 transferOwnerFunction; // function signature on contract to call and transfer ownership 
9:     }

File: SpigotedLine.sol
32:     uint8 public immutable defaultRevenueSplit;

File: LineFactory.sol
12:     uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment
13:     uint8 constant MAX_SPLIT = 100; // max % to take
14:     uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio

```

# INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

When we define internal functions to perform computation:

- The contract’s code size gets bigger
- The function call consumes more gas than executing it as an inlined function (part of the code, without the function call)

When it does not affect readability, it is recommended to inline functions in order to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
File: LineOfCredit.sol
167:     function _updateOutstandingDebt()
168:         internal
169:         returns (uint256 principal, uint256 interest)
170:     {

File: InterestRateCredit.sol
42:     function _accrueInterest(
43:         bytes32 id,
44:         uint256 drawnBalance,
45:         uint256 facilityBalance
46:     ) internal returns (uint256) {

File: MutualConsent.sol
38:     function _mutualConsent(address _signerOne, address _signerTwo) internal returns(bool) {

```