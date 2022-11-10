# low

## L-1 consider using two step owner/operator transfer in Spigot

to not accidentally transfer ownership/operator to the wrong address. Since these can potentially control a lot of other contracts.

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/spigot/Spigot.sol#L159-L161
```javascript
File: modules/spigot/Spigot.sol

159:    function updateOwner(address newOwner) external returns (bool) {
160:        return state.updateOwner(newOwner);
161:    }
```

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/spigot/Spigot.sol#L170-L172
```javascript
File: modules/spigot/Spigot.sol

170:    function updateOperator(address newOperator) external returns (bool) {
171:        return state.updateOperator(newOperator);
172:    }
```

## L-2 State changes after interactions

Doesn't follow checks effect interactions. If you really go out of your way as a borrower you could use this reentrancy to mess up the state of `ids` however that requires a lot of intentional effort to shoot yourself in the foot so I don't see it as any real issue.

https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/LineOfCredit.sol#L364
```javascript
File: modules/credit/LineOfCredit.sol

360:        LineLib.sendOutTokenOrETH(credit.token, borrower, amount); // interaction
361:
362:        emit Borrow(id, amount);
363:
364:        _sortIntoQ(id); // state change
```

## L-3 protocol doesn't support tokens with fee on transfer

Tokens like `STA`, `PAXG`. Also, `USDT` and `USDC` have the possiblilty to charge a fee in the future.

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/LineLib.sol#L69
```javascript
File: utils/LineLib.sol

68:        if(token != Denominations.ETH) { // ERC20
69:            IERC20(token).safeTransferFrom(sender, address(this), amount);
70:        } else { // ETH
```

The accounting assumes that `amount` has been transferred while it might be less. Hence tokens that have a fee would cause accounting errors. Since token used are decided by the lender, borrower and arbiter it might be good with a warning saying tokens with transfer fees are not supported.

Alternatively calculate the balance diff after transfer and use that for amount.

# non-crit

## NC-1 weird or misspelled comments
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84
```javascript
File: modules/credit/SpigotedLine.sol

84:      * @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent
```


https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L94-L95
```javascript
File: modules/escrow/Escrow.sol

94:     * @notice - allows  the lines arbiter to  enable thdeposits of an asset
95:     *        - gives  better risk segmentation forlenders
```

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/EscrowedLine.sol#L43
```javascript
File: modules/credit/EscrowedLine.sol

43:   * see SecuredlLine.liquidate
```

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/EscrowedLine.sol#L45
```javascript
File: modules/credit/EscrowedLine.sol

45:   * @dev priviliegad function. Do checks before calling.
```

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/EscrowedLine.sol#L74
```javascript
File: modules/credit/EscrowedLine.sol

74:   *(@dev priviliegad internal function.
```

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/modules/credit/EscrowedLine.sol#L83-L85
```javascript
File: modules/credit/EscrowedLine.sol

83:   * see SecuredlLine.rollover
84:   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
85:   *(@dev priviliegad internal function.
```

## NC-2 unreachable code

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L207
```javascript
File: utils/SpigotedLineLib.sol

205:        revert CallerAccessDenied();
206:
207:        return false;
```
https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotedLineLib.sol#L234
```javascript
File: utils/SpigotedLineLib.sol

234:        revert CallerAccessDenied();
235:
236:        return false;
```

## NC-3 use `type(uint256).max` instead of `111....35`

easier to read

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/EscrowLib.sol#L25-L26
```javascript
File: utils/EscrowLib.sol

25:    uint256 constant MAX_INT =
26:        115792089237316195423570985008687907853269984665640564039457584007913129639935;
```

and in tests

## NC-4 long line

GitHub starts using a scroll bar when the length is over 164 characters, the lines below should be split when they reach that length.

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/interfaces/ISpigotedLine.sol#L33
```javascript
File: interfaces/ISpigotedLine.sol

33:    * @notice - Claims revenue tokens from the Spigot, trades them for credit tokens via a Dex aggregator (Ox protocol) and uses the bought credit tokens to repay debt.
```

## NC-5 there is no way to update a `claimFunction`

Consider adding a function to update the `claimFunction` for the spigot so you don't have to remove and re-add the whole spigot just to change the `claimFunction`. Either if the `claimFunction` was wrong or if you want to switch from pull to push.