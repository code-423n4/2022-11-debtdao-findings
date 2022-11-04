# Increments/Decrements can be unchecked in For-Loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Consider wrapping with an unchecked block here:

[contracts/modules/credit/LineOfCredit.sol:179](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol) - for (uint256 i; i < len; ++i) {
[contracts/modules/credit/LineOfCredit.sol:203](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol) - for (uint256 i; i < len; ++i) {
[contracts/modules/credit/LineOfCredit.sol:520](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol) - for (uint256 i; i <= lastSpot; ++i) {

The change would be from:

for (uint256 i; i < numIterations; ++i) {

To:

for (uint256 i; i < numIterations;) {
// ...  
    unchecked { ++i; }
}

The same can be applied with decrements (which should use break when i == 0).

The risk of overflow is non-existent for uint256 here.

