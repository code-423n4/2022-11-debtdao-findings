1.
Title: Gas improvement on returning `collateralValue` value

Proof of Concept:
[EscrowLib.sol#L52](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L52)

Recommended Mitigation Steps:
by set `collateralValue` in returns L#51 and delete L#52 can save gas

```
    function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256 collateralValue) { //@audit-info: set here
        //@audit-info: delete L#52
        // gas savings
        uint256 length = self.collateralTokens.length;
```
________________________________________________________________________

2.
Title: Using `!=` in `require` statement is more gas efficient

Proof of Concept:
[EscrowLib.sol#L91](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91)
[EscrowLib.sol#L161](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161)

Recommended Mitigation Steps:
Change `> 0` to `!= 0`
________________________________________________________________________

3.
Title: Unused library

Proof of Concept:
[EscrowLib.sol#L22](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L22)
[SpigotedLine.sol#L23](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L23)
[LineOfCredit.sol#L17](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L17)

Recommended Mitigation Steps:
Remove it can save gas if it's unused
________________________________________________________________________

4.
Title: Unchecking arithmetics operations that can't underflow/overflow

Proof of Concept:
[EscrowLib.sol#L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164) Should be unchecked due to `if` condition in L#163
[EscrowLib.sol#L202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202) Should be unchecked due to `if` condition in L#200
[SpigotedLine.sol#L144](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L144) Should be unchecked due to `require` condition in L#143

Recommended Mitigation Steps:
Use `unchecked` can save gas
________________________________________________________________________

5.
Title: Gas savings for using solidity 0.8.10

impact:
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

Proof of Concept:
All contract in scope

Recommended Mitigation Steps:
Consider to upgrade pragma to at least 0.8.10.

Solidity 0.8.10 has a useful change which reduced gas costs of external calls
Reference: [here](https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/)
______________________________________________________________________

6.
Title: Consider remove empty block

impact:
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

Proof of Concept:
[SpigotedLine.sol#L272](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272)
________________________________________________________________________