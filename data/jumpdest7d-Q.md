# QA Report

## Suggested Improvements

| # | File | Suggestion | More Details |
|---|---|---|---|
| 1 | [ILineOfCredit.sol L110-111](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/interfaces/ILineOfCredit.sol#L110-L111), [ILineOfCredit.sol L127](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/interfaces/ILineOfCredit.sol#L127) | In `addCredit()` and `setRates()`, rename `drate` to `dRate` and `frate` to `fRate`. | Deposit Rate and Facility Rate parameters are named `dRate` and `fRate` elsewhere. Rename these two parameters to make it consistent. |
| 2 | [LineOfCredit.sol L31](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L31) | Rename `count` to `numCreditLines`. | This variable tracks the number of open lines in this contract. Consider renaming it to `numCreditLines` make its role more obvious. |
| 3 | [LineOfCredit.sol L33](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L33) | Rename `ids` to `openCreditLines`. | `ids` is an ordered list of credit lines that are open. Renaming it to `openCreditLines` would better reflect its purpose. |
| 4 | [LineOfCredit.sol L241](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L241), [LineOfCredit.sol L259](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L259) |  Remove `require` in `addCredit()` and `setRates()`. | The `require` call will always pass, as `InterestRate.setRate` will always return true. Remove the `require` statement and keep the call to `interestRate.setRate()`. |
| 5 | [SpigotedLineLib.sol L205](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L205), [SpigotedLineLib.sol L232](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L232) | Remove `return` statement from `releaseSpigot` and `sweep`. | The `return` statement in both of these functions is unreachable due to the `revert` statement before it. |
| 6 | [SpigotedLineLib.sol L215](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L215) | NatSpec return value documentation for `sweep` function is incorrect. | The NatSpec for the return value seems to be copied from `SpigotedLineLib.releaseSpigot`. Consider changing it to `@return - whether or not sweep was successful` |
| 7 | [ILineFactory.sol L7](https://github.com/debtdao/Line-of-Credit/blob/ab234ba796dec51638baf2632edf314fd3cf0c4e/contracts/interfaces/ILineFactory.sol#L7) | Rename `cratio` to `cRatio`. | Keep the parameter names consistent with other uses in the codebase, such as `dRate` and `fRate`. |

##  Message Improvements for `require` Statements
The following table shows usages of `require` that do not return error messages should the condition  fail.

| # | File | Function | Suggested Error Message |
|---|---|---|---|
| 8 | [EscrowedLine.sol L64](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L64) | `_getCollateralValue()` | `"failed to liquidate escrowed collateral"` |
| 9 | [EscrowedLine.sol L90](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/EscrowedLine.sol#L90) | `_rollover()` | `"failed to transfer line ownership"` |
| 10 | [LineOfCredit.sol L112](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L112) | `healthcheck()` | `"line not initialized"` |
| 11 | [LineOfCredit.sol L241](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L241), [LineOfCredit.sol L259](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L259) | `addCredit()`, `setRates()` | `"failed to set interest rates"` |
| 12 | [LineOfCredit.sol L326](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/LineOfCredit.sol#L326) | `depositAndRepay()` | `"repayment amount greater than total debt"` |
| 13 | [SpigotedLine.sol L62](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L62) | `constructor()` | `"invalid revenue split ratio"` |
| 14 | [SpigotedLine.sol L143](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L143) | `useAndRepay()` | `"amount exceeds unused token balance"` |
| 15 | [SpigotedLine.sol L160](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L160) | `claimAndTrade()` | `"only borrower"` |
| 16 | [SpigotedLine.sol L239](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/modules/credit/SpigotedLine.sol#L239) | `updateWhitelist()` | `"only arbiter"` |
| 17 | [EscrowLib.sol L91](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L91), [EscrowLib.sol L161](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L161), [EscrowLib.sol L198](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L198) | `addCollateral()`, `releaseCollateral()`, `liquidate()` | `"zero amount not allowed"` |
| 18 | [EscrowLib.sol L105](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L105) | `enableCollateral()` | `"only arbiter"` |
| 19 | [EscrowLib.sol L216](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L216) | `updateLine()` | `"only line"` |
| 20 | [SpigotedLineLib.sol L147](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotedLineLib.sol#L147) | `updateOwner()` | `"failed to update spigot owner"` |
| 21 | [SpigotLib.sol L96](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L96) | `claimRevenue()` | `"failed to send tokens to treasury"` |
| 22 | [SpigotLib.sol L128](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L128) | `addSpigot()` | `"only revenue contract"` |
| 23 | [SpigotLib.sol L130](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L130) | `addSpigot()` | `"spigot already exists"` |
| 24 | [SpigotLib.sol L155](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L155) | `removeSpigot()` | `"failed to transfer revenue contract ownership"` |
| 25 | [SpigotLib.sol L180](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L180), [SpigotLib.sol L189](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L189), [SpigotLib.sol L201](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/SpigotLib.sol#L201) | `updateOwner()`, `updateOperator()`, `updateTreasury()` | `"invalid target address"` |


## Spelling Errors

The following spelling errors and misplaced characters were found in one or more source files. Search for the Spelling Error term to find occurrences. Consider correcting errors found in NatSpec documentation first since this will improve the developer experience for end callers.

| # | Spelling Error | Suggested Fix |
|---|---|---|
| 26 | `*(@dev priviliegad` | `* @dev privileged` |
| 27 | `SecuredlLine` | `SecuredLine ` |
| 28 | `swithc` | `switch` |
| 29 | `repyment` | `repayment` |
| 30 | `MutualConsentRegistsered` | `MutualConsentRegistered` |
| 31 | `renego` | *unknown* |
| 32 | `repais` | `repaid` |
| 33 | `usefull` | `useful` |
| 34 | `non-rentrant` | `non-reentrant` |
| 35 | `priviliges` | `privileges` |
| 36 | `debitor` | `debtor` |
| 37 | `diferent` | `different` |
| 38 | `demoninated` | `denominated` |
| 39 | `fuly` | `fully` |
| 40 | `LineofCredit` | `LineOfCredit` |
| 41 | `interfer` | `interfere` |
| 42 | `itselgf` | `itself` |
| 43 | `transfered` | `transferred` |
| 44 | `thdeposits` | `the deposits` |
| 45 | `forlenders` | `for lenders` |
| 46 | `acrrued` | `accrued` |
| 47 | `revenuContract` | `revenueContract` |
| 48 | `paramteter` | `parameter` |
| 49 | `bwithdrawn` | `withdrawn` |
| 50 | `interset` | `interest` |
| 51 | `sendingtokens/ETH` | `sending tokens/ETH` |
| 52 | `adddress` | `address` |
| 53 | `revenuw` | `revenue` |


## NatSpec Errors

Any other error found in NatSpec that aren't related to spelling.
| # | File | Error |
|---|---|---|
| 54 | [ISecuredLine.sol L16](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/interfaces/ISecuredLine.sol#L16) | Incomplete return value description. |