# QA Report

The protocol functionality was easy to follow thanks to a thorough documentation with detailed flow diagrams and a well commented code base

A few logic issues were found. They are minor but are worth taking a look at, given that the protocol has created a deFi primitive - the Line of Credit - and composability with external contracts is important. 


# L01 - Denial Of Service if credit lines number too high, making repayments impossible
https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/LineOfCredit.sol#L472
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L498
https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/CreditListLib.sol#L51-L54
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L449

The current logic is that `_close()` checks that a credit line is fully repaid and removes it, by doing [a call](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L498) to `removePosition`.

The issue is that `removePosition` does not actually remove the `id` from the `ids` array, it simply set it to `0`: the `delete` statement in solidity set an element to `0` but does not remove it from the array.

On each `LineOfCredit._repay()` call, a call to `stepQ` is made, which loops through the entire ids array to re-order it.

This means that if too many credit lines are added, the `stepQ` calls will always revert, essentially preventing a borrower from repaying their debts.

Each additional credit line adds roughly ~5,000 gas (1 `SLOAD` and 1 `SSTORE`) to a `_repay()` call. Given the 30M gas block limit, this means DoS will happen if a Line of Credit `ids` exceeds a certain number `M` between `5,000` and `6,000`.

After discussion with the team, this number of credit lines was however deemed unlikely given that there can be only one credit line per token.


## Impact

Low

## Proof Of Concept

The borrower and the lenders agree on the first credit line. The borrower calls `borrow()` and receives a loan on this first credit line.
The lenders and the borrowers agree to add N credit lines, and call `addCredit()`, pushing `N` credits to `ids`.

The borrower tries to call `depositAndClose()` to close the first credit line, but the call to `_repay()` will revert, as the `stepQ` call will run out of the gas before finishing to loop through all the `ids` of the array.

## Tools Used

Manual Analysis


## Mitigation

Consider setting a MAX_LINES that `ids.length` cannot exceed, preventing too many credit lines from being created.

# L02 - `_close()` should not be able to close a specific `id` credit line

As per the docs:

```
Can a Borrower chose to repay any debt in any order?
No. The app automatically selects which credit line can be repaid using a first-in-first-out (FIFO) repayment queue based on the order in which credit lines are drawn down
```

The issue is that `close()` takes an `id` argument, meaning it technically breaks this protocol requirement if called for any `id` other than `ids[0]`.
Now, because of [this credit.principal check](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L484), it is actually impossible to close any id other than the first one - because it is only possible to `repay` the full debt through `depositAndClose` and `depositAndRepay`, which always repay the first `id` in the array `ids`.

`close()` can hence only work for `id = ids[0]` anyway.

Because of the protocol desire for it to be composable, this can open risks if a child contract inheriting `LineOfCredit` were to give the possibility to repay full debt of any `id` credit line. This would mean a borrower could repay and close in any order.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Modify both `close()` and `_close()`, so that they can only close the first credit line in queue

```diff
-388:     function close(bytes32 id) external payable override returns (bool) {
+388:     function close() external payable override returns (bool) {
+            bytes32 id = ids[0];
389:         Credit memory credit = credits[id];
...
-406:         _close(credit, id);
+406:         _close(credit);
```

```diff
-483: function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
+483: function _close(Credit memory credit) internal virtual returns (bool) {
+            bytes32 id = ids[0];
```

# L03 - `close` underflow if called when `count == 0`

When deleting a position by calling `close`, the internal `_close` call decreases `count` - the state variable representing the open credit lines.

The issue is that the decrement is unchecked. So if a `borrower` calls `close()` when `count == 0`, the operation will underflow and `count` will be equal to `type(uint256).max`.

This does not leave to any direct vulnerability, as all `credits` will be empty and the Line status will be `REPAID` anyway, but this does mean the `counts()` function will return `count == type(uint256).max`, which can be misleading to any external contract using the `LineOfCredit` API, and even open to vulnerabilities if there is logic performed based on the reading of `count`.

## Impact

Low

## Proof of Concept

Add the following test to `LineOfCredit.t.sol`:

```solidity
function test_can_underflow_count() public {
    _addCredit(address(supportedToken1), 1 ether);
    bytes32 id = line.ids(0);
    hoax(borrower);
    line.borrow(id, 1 ether);
    hoax(borrower);
    line.depositAndRepay(1 ether);
    (uint p, uint i) = line.updateOutstandingDebt();
    assertEq(p + i, 0, "Line outstanding credit should be 0");
    hoax(borrower);
    line.close(id);

    // @audit borrower calls `count()` again
    hoax(borrower);
    line.close(id);

    // @audit count underflowed
    (uint count, uint len)= line.counts();
    assertEq(count, type(uint256).max, "count should be max");
}
```

## Tools Used

Manual Analysis, Foundry

## Mitigation

Leave the underflow check when decrementing `count`.

```diff
-499:      unchecked { --count; }
+499:      --count;
```

You can also add the `whileBorrowing` modifier to `close()`, to revert calls when `count == 0`

# L04 - Logic error in `SpigotedLine.releaseSpigot()`

`ISpigotedLine` describes `releaseSpigot()` as follow:

```solidity
95:     * @notice - Transfers ownership of the entire Spigot from its then Owner to either the Borrower (if a Line of Credit has been been fully repaid) 
96:                 or to the Arbiter (if the Line of Credit is liquidatable).
97:     * @dev    - callable by borrower + arbiter

```

The function [calls](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L194) the `releaseSpigot()` method of `SpigotedLineLib`. This method is described as:

```solidity
191:    * @dev    - callable by anyone 
```

This is the first problem: a mismatch in definitions between `ISpigotedLine` saying it should only be call by the arbiter or borrower, and the library stating there is no access control.

The second issue is that while both descriptions state the ownership should be transferred to either the borrower (if REPAID) or the arbiter (if liquidatable), the function actually transfers it to a `to` parameter that can be any address.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

The following mitigation ensures the spigot ownership can only be transferred to either the arbiter or the borrower.
You should also update `ISpigotedLine` and `SpigotedLineLib` so that the access control of the function is the same in both descriptions.

```diff
194:     function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {
195:         if (status == LineLib.STATUS.REPAID &&  msg.sender == borrower) {
-196:           if(!ISpigot(spigot).updateOwner(to)) { revert ReleaseSpigotFailed(); }
+196:           if(!ISpigot(spigot).updateOwner(borrower)) { revert ReleaseSpigotFailed(); }
197:           return true;
198:         }
199: 
200:         if (status == LineLib.STATUS.LIQUIDATABLE && msg.sender == arbiter) {
-201:           if(!ISpigot(spigot).updateOwner(to)) { revert ReleaseSpigotFailed(); }
+201:           if(!ISpigot(spigot).updateOwner(arbiter)) { revert ReleaseSpigotFailed(); }
202:           return true;
203:         }
```

# L05 - Logic error in `SpigotedLine.sweep()`

`ISpigotedLine` describes `sweep()` as follow:

```
104:    * @notice - sends unused tokens to borrower if REPAID or arbiter if LIQUIDATABLE or INSOLVENT

106:    * @dev    - callable by anyone 
```

But the function [calls](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L259) the `sweep()` method of `SpigotedLineLib` with a `to` parameter that can be any address, not just the borrower or the arbiter.

The function also only transfers the amount to `to` if the caller is the borrower and the line is repaid, or if the caller is the arbiter and the status is LIQUIDATABLE or INSOLVENT. Any other msg.sender would result in the call [reverting](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L232).

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Update `SpigotedLineLib.sweep()` to suit the description in `ISpigotedLine`:

the function should:

- be callable by anyone.
- transfers `amount` to `borrower` if the status is `REPAID`.
- transfers `amount` to `arbiter` if the status is `LIQUIDATABLE` or `INSOLVENT`.

```diff
217:     function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {
218:         if(amount == 0) { revert UsedExcessTokens(token, 0); }
219: 
-220:         if (status == LineLib.STATUS.REPAID && msg.sender == borrower) {
-221:             return LineLib.sendOutTokenOrETH(token, to, amount);
-222: 
+220:         if (status == LineLib.STATUS.REPAID) {
+221:             return LineLib.sendOutTokenOrETH(token, borrower, amount);
223:         }
224: 
-225:         if (
-226:           (status == LineLib.STATUS.LIQUIDATABLE || status == LineLib.STATUS.INSOLVENT) &&
-227:           msg.sender == arbiter
-228:         ) {
-229:             return LineLib.sendOutTokenOrETH(token, to, amount);
+225:         if (
+226:           (status == LineLib.STATUS.LIQUIDATABLE || status == LineLib.STATUS.INSOLVENT)){
+229:             return LineLib.sendOutTokenOrETH(token, arbiter, amount);
230:         }
231: 
-232:         revert CallerAccessDenied();
```

# L06 - Last statement in `SpigotedLineLib.sweep()` is never reached

The `sweep` method of the `SpigotedLineLib` library is used to sweep remaining tokens in the Spigot.

If the conditions line 220 and 226 are not met, the call [reverts](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L232).

There is however a `return false` statement line 234, after the revert statement.

This `return` statement will never be reached, as the call will revert beforehand every time.

## Impact

Low

## Tools Used

Manual Analysis

## Mitigation

Either have the function revert and remove `return false`, or remove the revert() statement.

# NC01 - typos


- [demoninated](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L213) -> denominated