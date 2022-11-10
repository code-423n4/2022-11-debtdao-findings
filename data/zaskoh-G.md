**# use {unchecked} in for loops to save ~30-40gas per loop**

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203)
```bash
203: for (uint256 i; i < len; ++i) {
    
Example:
for (uint256 i; i < len;) {            
    id = ids[i];
    Credit memory credit = credits[id];
    credits[id] = _accrue(credit, id);
    unchecked{++i;}
}
```

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179)
```bash
179: for (uint256 i; i < len; ++i) {
```

[contracts/modules/credit/LineOfCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520)
```bash
520: for (uint256 i; i <= lastSpot; ++i) {
```

[contracts/utils/EscrowLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57)
```bash
57: for (uint256 i; i < length; ++i) {
```

[contracts/utils/CreditListLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L51)
```bash
51: for(uint i = 1; i < len; ++i) {
```
---

**# use custom errors instead of revert strings**

[contracts/modules/interest-rate/InterestRateCredit.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L26)
```bash
26: require(
            msg.sender == lineContract,
            "InterestRateCred: only line contract."
    );
```

---

**# use calldata in external calls if possible**

[contracts/modules/spigot/Spigot.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125)
+
[contracts/utils/SpigotLib.sol](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125)
```bash
125: Setting memory setting (Spigot.sol)
125: ISpigot.Setting memory setting

can botj replaced with calldata to save ~80-120gas per call
```
