
## Cache `.length` in loops
**Summary:** Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

```solidity
contracts/modules/credit/LineOfCredit.sol:31:    uint256 private count; // amount of open credit lines on a Line of Credit facility. ids.length includes null items
contracts/modules/credit/LineOfCredit.sol:118:        return (count, ids.length);
contracts/modules/credit/LineOfCredit.sol:172:        uint256 len = ids.length;
contracts/modules/credit/LineOfCredit.sol:201:        uint256 len = ids.length;
contracts/modules/credit/LineOfCredit.sol:517:        uint256 lastSpot = ids.length - 1;
contracts/utils/CreditListLib.sol:21:      uint256 len = ids.length;
contracts/utils/CreditListLib.sol:41:      uint256 len = ids.length ;
contracts/utils/EscrowLib.sol:54:        uint256 length = self.collateralTokens.length;
contracts/utils/EscrowLib.sol:119:                bool is4626 = tokenAddrBytes.length > 0 && passed;
contracts/utils/EscrowLib.sol:134:                if (decimalBytes.length > 0 && successDecimals) {
```
## Use `!= 0` instead of `> 0`
```solidity
contracts/modules/credit/LineOfCredit.sol:132:        if (block.timestamp >= deadline && count > 0) {
contracts/modules/credit/LineOfCredit.sol:398:        if(facilityFee > 0) {
contracts/modules/credit/LineOfCredit.sol:484:        if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
contracts/modules/credit/LineOfCredit.sol:487:        if (credit.deposit + credit.interestRepaid > 0) {
contracts/modules/credit/LineOfCredit.sol:526:                  credits[id].principal > 0 //`id` should be placed before `p` 
contracts/utils/EscrowLib.sol:91:        require(amount > 0);
contracts/utils/EscrowLib.sol:119:                bool is4626 = tokenAddrBytes.length > 0 && passed;
contracts/utils/EscrowLib.sol:134:                if (decimalBytes.length > 0 && successDecimals) {
contracts/utils/EscrowLib.sol:161:        require(amount > 0);
contracts/utils/EscrowLib.sol:198:        require(amount > 0);
```

## `<x> += <y>` costs more gas than `<x> = <x> + <y>` 
```solidity
contracts/modules/credit/LineOfCredit.sol:192:            principal += _p;
contracts/modules/credit/LineOfCredit.sol:193:            interest += _i;
contracts/modules/credit/LineOfCredit.sol:276:        credit.deposit += amount;
contracts/modules/credit/LineOfCredit.sol:351:        credit.principal += amount;
contracts/modules/credit/SpigotedLine.sol:122:            unusedTokens[credit.token] -= repaid - newTokens;
contracts/modules/credit/SpigotedLine.sol:125:            unusedTokens[credit.token] += newTokens - repaid;
contracts/modules/credit/SpigotedLine.sol:144:      unusedTokens[credit.token] -= amount;
contracts/modules/credit/SpigotedLine.sol:172:        unusedTokens[targetToken] += newTokens;
contracts/utils/EscrowLib.sol:75:                collateralValue += CreditLib.calculateValue(
contracts/utils/EscrowLib.sol:96:        self.deposited[token].amount += amount;
contracts/utils/EscrowLib.sol:164:        self.deposited[token].amount -= amount;
contracts/utils/EscrowLib.sol:202:        self.deposited[token].amount -= amount;
contracts/utils/CreditLib.sol:177:          credit.interestAccrued -= amount;
contracts/utils/CreditLib.sol:178:          credit.interestRepaid += amount;
contracts/utils/CreditLib.sol:186:          credit.principal -= principalPayment;
contracts/utils/CreditLib.sol:187:          credit.interestRepaid += interest;
contracts/utils/CreditLib.sol:216:          amount -= interest;
contracts/utils/CreditLib.sol:218:          credit.deposit -= amount;
contracts/utils/CreditLib.sol:227:          credit.interestRepaid -= amount;
contracts/utils/CreditLib.sol:258:      credit.interestAccrued += accruedToken;
```

## Use `calldata` instead of `memory` when used only for reading
```solidity
contracts/modules/credit/LineOfCredit.sol:218:    function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {
contracts/modules/credit/LineOfCredit.sol:465:    function _repay(Credit memory credit, bytes32 id, uint256 amount)
contracts/modules/credit/LineOfCredit.sol:483:    function _close(Credit memory credit, bytes32 id) 
contracts/utils/SpigotLib.sol:125:    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
```

## Use `variable == false|0` -> `!variable` or `variable ==  true` -> `variable`
```solidity
contracts/modules/credit/LineOfCredit.sol:84:        if(count == 0 || credits[ids[0]].principal == 0) { revert NotBorrowing(); }
contracts/modules/credit/LineOfCredit.sol:173:        if (len == 0) return (0, 0);
contracts/modules/credit/LineOfCredit.sol:472:        if (credit.principal == 0) ids.stepQ();
contracts/modules/credit/LineOfCredit.sol:502:        if (count == 0) { _updateStatus(LineLib.STATUS.REPAID); }
contracts/utils/EscrowLib.sol:38:        if (debtValue == 0) return MAX_INT;
contracts/utils/EscrowLib.sol:39:        if (collateralValue == 0) return 0;
contracts/utils/SpigotedLineLib.sol:85:        if(tokensBought == 0) { revert TradeFailed(); } // ensure tokens bought
contracts/utils/SpigotedLineLib.sol:218:        if(amount == 0) { revert UsedExcessTokens(token, 0); }
contracts/utils/SpigotLib.sol:51:        if(claimed == 0) { revert NoRevenue(); }
contracts/utils/SpigotLib.sol:113:        if(claimed == 0) { revert ClaimFailed(); }
```


## `10eX` use less gas then `10**X`
```solidity
contracts/utils/EscrowLib.sol:42:        uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals
contracts/utils/CreditLib.sol:117:    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```

## ` >=` COSTS LESS GAS THAN `>`
The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde 
```solidity
contracts/modules/credit/LineOfCredit.sol:132:        if (block.timestamp >= deadline && count > 0) {
contracts/modules/credit/LineOfCredit.sol:349:        if(amount > credit.deposit - credit.principal) { revert NoLiquidity(); }
contracts/modules/credit/LineOfCredit.sol:398:        if(facilityFee > 0) {
contracts/modules/credit/LineOfCredit.sol:484:        if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
contracts/modules/credit/LineOfCredit.sol:487:        if (credit.deposit + credit.interestRepaid > 0) {
contracts/modules/credit/LineOfCredit.sol:526:                  credits[id].principal > 0 //`id` should be placed before `p` 
contracts/modules/credit/SpigotedLine.sol:118:        if (repaid > debt) repaid = debt;
contracts/modules/credit/SpigotedLine.sol:120:        if (repaid > newTokens) {
contracts/modules/factories/LineFactory.sol:91:        if (coreParams.revenueSplit > MAX_SPLIT) {
contracts/utils/EscrowLib.sol:91:        require(amount > 0);
contracts/utils/EscrowLib.sol:119:                bool is4626 = tokenAddrBytes.length > 0 && passed;
contracts/utils/EscrowLib.sol:134:                if (decimalBytes.length > 0 && successDecimals) {
contracts/utils/EscrowLib.sol:161:        require(amount > 0);
contracts/utils/EscrowLib.sol:198:        require(amount > 0);
contracts/utils/CreditLib.sol:210:      if(amount > credit.deposit - credit.principal + credit.interestRepaid) {
contracts/utils/CreditLib.sol:214:      if (amount > credit.interestRepaid) {
contracts/utils/SpigotedLineLib.sol:100:        if(oldClaimTokens > newClaimTokens) {
contracts/utils/SpigotedLineLib.sol:105:          if(diff > unused) revert UsedExcessTokens(claimToken,  unused); 
contracts/utils/SpigotLib.sol:55:        if(claimed > MAX_REVENUE) claimed = MAX_REVENUE;
contracts/utils/SpigotLib.sol:95:        if(claimed > escrowedAmount) {
contracts/utils/SpigotLib.sol:134:        if(setting.ownerSplit > MAX_SPLIT) { revert BadSetting(); }
contracts/utils/SpigotLib.sol:169:      if(ownerSplit > MAX_SPLIT) { revert BadSetting(); }
```

## Use `abi.encodePacked` for gas optimization 
Changing the abi.encode function to abi.encodePacked  can save gas since the abi.encode function pads extra null bytes at the end of the call data, which is unnecessary. Also, in general, abi.encodePacked is more gas-efficient.
```solidity
contracts/utils/CreditLib.sol:69:    return keccak256(abi.encode(line, lender, token));
```

## Use custom errors to save deployment and runtime costs in case of revert
Instead of using strings for error messages (e.g., `require(msg.sender == owner, “unauthorized”)`), you can use custom errors to reduce both deployment and runtime gas costs. In addition, they are very convenient as you can easily pass dynamic information to them.
```solidity
contracts/modules/credit/LineOfCredit.sol:112:        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
contracts/modules/credit/LineOfCredit.sol:241:        require(interestRate.setRate(id, drate, frate));
contracts/modules/credit/LineOfCredit.sol:259:        require(interestRate.setRate(id, drate, frate));
contracts/modules/credit/LineOfCredit.sol:326:        require(amount <= credit.principal + credit.interestAccrued);
contracts/modules/credit/EscrowedLine.sol:64:    require(escrow_.liquidate(amount, targetToken, to));
contracts/modules/credit/EscrowedLine.sol:90:    require(escrow.updateLine(newLine));
contracts/modules/credit/SpigotedLine.sol:62:        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
contracts/modules/credit/SpigotedLine.sol:143:      require(amount <= unusedTokens[credit.token]);
contracts/modules/credit/SpigotedLine.sol:160:        require(msg.sender == borrower);
contracts/modules/credit/SpigotedLine.sol:239:        require(msg.sender == arbiter);
contracts/modules/interest-rate/InterestRateCredit.sol:26:        require(
contracts/utils/EscrowLib.sol:91:        require(amount > 0);
contracts/utils/EscrowLib.sol:105:        require(msg.sender == ILineOfCredit(self.line).arbiter());
contracts/utils/EscrowLib.sol:161:        require(amount > 0);
contracts/utils/EscrowLib.sol:198:        require(amount > 0);
contracts/utils/EscrowLib.sol:216:      require(msg.sender == self.line);
contracts/utils/SpigotedLineLib.sol:147:      require(ISpigot(spigot).updateOwner(newLine));
contracts/utils/SpigotLib.sol:96:            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
contracts/utils/SpigotLib.sol:128:        require(revenueContract != address(this));
contracts/utils/SpigotLib.sol:130:        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
contracts/utils/SpigotLib.sol:155:        require(success);
contracts/utils/SpigotLib.sol:180:        require(newOwner != address(0));
contracts/utils/SpigotLib.sol:189:        require(newOperator != address(0));
contracts/utils/SpigotLib.sol:201:        require(newTreasury != address(0));
```


## `++i`/`i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops :
```solidity
contracts/modules/credit/LineOfCredit.sol:179:        for (uint256 i; i < len; ++i) {
contracts/modules/credit/LineOfCredit.sol:203:        for (uint256 i; i < len; ++i) {
contracts/modules/credit/LineOfCredit.sol:520:        for (uint256 i; i <= lastSpot; ++i) {
contracts/utils/CreditListLib.sol:23:      for(uint256 i; i < len; ++i) {
contracts/utils/CreditListLib.sol:51:        for(uint i = 1; i < len; ++i) {
contracts/utils/EscrowLib.sol:57:        for (uint256 i; i < length; ++i) {
```
