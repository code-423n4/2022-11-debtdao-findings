# 1. [G-1] For loops: increments in for loop can be uncheck to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block

    File contracts/modules/LineOfCredit.sol, line 179:     for (uint256 i; i < len; ++i) {
    File contracts/modules/LineOfCredit.sol, line 203:     for (uint256 i; i < len; ++i) {
    File contracts/modules/LineOfCredit.sol, line 520:     for (uint256 i; i <= lastSpot; ++i) {

    File contracts/utils/EscrowLib.sol, line 57:     for (uint256 i; i < length; ++i) {

The code would go from:

    for (uint256 i; i < numIterations; i++) { 
      ...
    }

to:

    for (uint256 i; i < numIterations;) { 
      ...
      unchecked { ++i; }  
    }

# 2. [G-2] Variables: "Constant" expressions are expressions, not constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

Instances include:

    File contracts/interest-rate/InterestRateCredit.sol, line 11:             uint256 constant INTEREST_DENOMINATOR = ONE_YEAR * BASE_DENOMINATOR;

    File contracts/utils/SpigotLib.sol, line 27:     uint256 constant MAX_REVENUE = type(uint256).max / MAX_SPLIT;

Change these expressions from `constant` to `immutable` and implement the calculation in the constructor, or hardcode these values in the constants and add a comment to say how the value was calculated.

# 3. [G-3] Arithmetics: uncheck blocks for arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible, some gas can be saved by using an `unchecked` block.

I suggest wrapping with an `unchecked` block here:

    File contracts/modules/credit/LineOfCredit.sol, line 192 - 193:     principal += _p;
                                                                                                           interest += _i;
    File contracts/modules/credit/LineOfCredit.sol, line 276:     credit.deposit += amount;
    File contracts/modules/credit/LineOfCredit.sol, line 304:     uint256 totalOwed = credit.principal + credit.interestAccrued;
    File contracts/modules/credit/LineOfCredit.sol, line 326:     require(amount <= credit.principal + credit.interestAccrued);
    File contracts/modules/credit/LineOfCredit.sol, line 351:     credit.principal += amount;

    File contracts/utils/SpigotLib.sol, line 38:     claimed = existingBalance - self.escrowed[token];
    File contracts/utils/SpigotLib.sol, line 47:     claimed = LineLib.getBalance(token) - existingBalance;

    File contracts/utils/SpigotLib.sol, line 90-92:     uint256 escrowedAmount = claimed * self.settings[revenueContract].ownerSplit / 100;
                                                                               self.escrowed[token] = self.escrowed[token] + escrowedAmount;

    File contracts/utils/SpigotLib.sol, line 114-115:     
                        uint256 repaid = newTokens + unusedTokens[credit.token];
                        uint256 debt = credit.interestAccrued + credit.principal;

    File contracts/utils/SpigotLib.sol, line 120-127:     
                        if (repaid > newTokens) {
                            // using bought + unused to repay line
                            unusedTokens[credit.token] -= repaid - newTokens;
                        } else {
                            // high revenue and bought more than we need
                            unusedTokens[credit.token] += newTokens - repaid;
                        }

    File contracts/utils/SpigotLib.sol, line 144:     unusedTokens[credit.token] -= amount;
    File contracts/utils/SpigotLib.sol, line 172:     unusedTokens[targetToken] += newTokens;

    File contracts/modules/interest-rate/InterestRateCredit.sol, line 48:     uint256 timespan = block.timestamp - rate.lastAccrued;
    File contracts/modules/interest-rate/InterestRateCredit.sol, line 70:     return (bpsRate * balance * timespan) / INTEREST_DENOMINATOR;

    File contracts/utils/EscrowLib.sol, line 36:     uint256 debtValue =  principal + interest;

    File contracts/utils/EscrowLib.sol, line 42-43:     
                    uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals
                    return ((_numerator / debtValue) + 5) / 10;

    File contracts/utils/EscrowLib.sol, line 96:     self.deposited[token].amount += amount;
    File contracts/utils/EscrowLib.sol, line 202:     self.deposited[token].amount -= amount;

# 4. [G-4] Variables: Cache read variables in memory will save gas

    File contracts/modules/credit/LineOfCredit.sol, line 179 - 180:     
                    for (uint256 i; i < len; ++i) {
                        id = ids[i];
                        ...
                    }
    File contracts/modules/credit/LineOfCredit.sol, line 203 - 204:     
                    for (uint256 i; i < len; ++i) {
                        id = ids[i];
                        ...
                    }
    File contracts/modules/credit/LineOfCredit.sol, line 520 - 521:     
                    for (uint256 i; i <= lastSpot; ++i) {
                        id = ids[i];
                        ...
                    }

Here I suggest create a memory variable of the storage variable `ids` before of the for loop, and use it instead.

# 5. [G-5] Parameter: If we are not modifying the passed parameter we should pass it as `calldata` because `calldata` is more gas efficient than `memory` or `storage`

I suggest using `calldata` instead of `storage` here:

    File contracts/utils/SpigotLib.sol, line 29:     function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)
    File contracts/utils/SpigotLib.sol, line 61:     function operate(SpigotState storage self, address revenueContract, bytes calldata data)
    File contracts/utils/SpigotLib.sol, line 216:     function getEscrowed(SpigotState storage self, address token)
    File contracts/utils/SpigotLib.sol, line 221:     function isWhitelisted(SpigotState storage self, bytes4 func)
    File contracts/utils/SpigotLib.sol, line 227:     function getSetting(SpigotState storage self, address revenueContract)

I suggest using `calldata` instead of `memory` here:
    File contracts/utils/SpigotLib.sol, line 483:     function _close(Credit memory credit, bytes32 id)

    File contracts/utils/CreditListLib.sol, line 74:     
            function getOutstandingDebt(
                ILineOfCredit.Credit memory credit,
                bytes32 id,
                address oracle,
                address interestRate
            )

    File contracts/utils/SpigotLib.sol, line 125:     function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting)
