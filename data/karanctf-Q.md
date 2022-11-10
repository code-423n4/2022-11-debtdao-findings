## Remove 1 from 1 * 10
contracts/utils/CreditLib.sol:117:    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
## Use scientific notation (e.g. `10e18`) rather than exponentiation (e.g. `10**18`)
```solidity
contracts/utils/EscrowLib.sol:42:        uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals
contracts/utils/CreditLib.sol:117:    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```

## The immutable and constant variables should be written in uppercase.
**Details**: According to the [Solidity](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Style](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[Guide](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)[ ](https://docs.soliditylang.org/en/v0.8.10/style-guide.html#constants)constants should be named with all capital letters with underscores separating words. Immutables can be treated similar to constants because they are replaced by their assigned values during contract construction.
```solidity
contracts/modules/credit/LineOfCredit.sol:21:    uint256 public immutable deadline;
contracts/modules/credit/LineOfCredit.sol:23:    address public immutable borrower;
contracts/modules/credit/LineOfCredit.sol:25:    address public immutable arbiter;
contracts/modules/credit/LineOfCredit.sol:27:    IOracle public immutable oracle;
contracts/modules/credit/LineOfCredit.sol:29:    InterestRateCredit public immutable interestRate;
contracts/modules/credit/EscrowedLine.sol:14:  IEscrow immutable public escrow;
contracts/modules/credit/SpigotedLine.sol:26:    ISpigot public immutable spigot;
contracts/modules/credit/SpigotedLine.sol:29:    address payable public immutable swapTarget;
contracts/modules/credit/SpigotedLine.sol:32:    uint8 public immutable defaultRevenueSplit;
contracts/modules/factories/LineFactory.sol:10:    IModuleFactory immutable factory;
contracts/modules/factories/LineFactory.sol:12:    uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment
contracts/modules/factories/LineFactory.sol:14:    uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio
contracts/modules/factories/LineFactory.sol:16:    address public immutable arbiter;
contracts/modules/factories/LineFactory.sol:17:    address public immutable oracle;
contracts/modules/factories/LineFactory.sol:18:    address public immutable swapTarget;
contracts/modules/interest-rate/InterestRateCredit.sol:13:    address immutable lineContract;
contracts/modules/escrow/Escrow.sol:24:    uint32 public immutable minimumCollateralRatio;
contracts/modules/escrow/Escrow.sol:27:    address public immutable oracle;
contracts/modules/escrow/Escrow.sol:29:    address public immutable borrower;
```

## `require()`/`revert()` statements should have descriptive reason strings
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
## Open TODOs
```solidity
contracts/modules/factories/LineFactory.sol:140:        // TODO: test
contracts/modules/factories/LineFactory.sol:145:        // TODO: test
```

## Use of `Block.timestamp`
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
```solidity
contracts/modules/credit/LineOfCredit.sol:58:        deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility
contracts/modules/credit/LineOfCredit.sol:132:        if (block.timestamp >= deadline && count > 0) {
contracts/modules/interest-rate/InterestRateCredit.sol:48:        uint256 timespan = block.timestamp - rate.lastAccrued;
contracts/modules/interest-rate/InterestRateCredit.sol:50:        rates[id].lastAccrued = block.timestamp;
contracts/modules/interest-rate/InterestRateCredit.sol:82:            lastAccrued: block.timestamp
```


## `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
```solidity
contracts/utils/MutualConsent.sol-41-        address nonCaller = _getNonCaller(_signerOne, _signerTwo);
contracts/utils/MutualConsent.sol:45:        bytes32 expectedHash = keccak256(abi.encodePacked(msg.data, nonCaller));
contracts/utils/MutualConsent.sol-47-        if (!mutualConsents[expectedHash]) {
contracts/utils/MutualConsent.sol:48:            bytes32 newHash = keccak256(abi.encodePacked(msg.data, msg.sender));
```


##  Inconsistent function return patterns affects readability
**Summary**: Some functions explicitly return a value while others assign a value to a named return variable.
**Details**: If many functions explicitly return a value, one may incorrectly infer that functions assigning a value to a named return variable are not actually returning anything.
```solidity
contracts/modules/oracle/Oracle.sol:22:    function getLatestAnswer(address token) external returns (int) {
contracts/modules/credit/LineOfCredit.sol:64:    function init() external virtual returns(LineLib.STATUS) {
contracts/modules/credit/LineOfCredit.sol:69:    function _init() internal virtual returns(LineLib.STATUS) {
contracts/modules/credit/LineOfCredit.sol:110:    function healthcheck() external returns (LineLib.STATUS) {
contracts/modules/credit/LineOfCredit.sol:117:    function counts() external view returns (uint256, uint256) {
contracts/modules/credit/LineOfCredit.sol:121:    function _healthcheck() internal virtual returns (LineLib.STATUS) {
contracts/modules/credit/LineOfCredit.sol:143:    function declareInsolvent() external whileBorrowing returns(bool) {
contracts/modules/credit/LineOfCredit.sol:157:    function _canDeclareInsolvent() internal virtual returns(bool) {
contracts/modules/credit/LineOfCredit.sol:163:    function updateOutstandingDebt() external override returns (uint256, uint256) {
contracts/modules/credit/LineOfCredit.sol:200:    function accrueInterest() external override returns(bool) {
contracts/modules/credit/LineOfCredit.sol:218:    function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {
contracts/modules/credit/LineOfCredit.sol:388:    function close(bytes32 id) external payable override returns (bool) {
contracts/modules/credit/LineOfCredit.sol:421:    function _updateStatus(LineLib.STATUS status_) internal returns(LineLib.STATUS) {
contracts/modules/credit/LineOfCredit.sol:483:    function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
contracts/modules/credit/LineOfCredit.sol:516:    function _sortIntoQ(bytes32 p) internal returns (bool) {
contracts/modules/credit/EscrowedLine.sol:25:  function _init() internal virtual returns(LineLib.STATUS) {
contracts/modules/credit/EscrowedLine.sol:34:  function _healthcheck() virtual internal returns(LineLib.STATUS) {
contracts/modules/credit/EscrowedLine.sol:77:  function _canDeclareInsolvent() internal virtual returns(bool) {
contracts/modules/credit/EscrowedLine.sol:89:  function _rollover(address newLine) internal virtual returns(bool) {
contracts/modules/credit/SecuredLine.sol:37:  function _init() internal override(SpigotedLine, EscrowedLine) virtual returns(LineLib.STATUS) {
contracts/modules/credit/SecuredLine.sol:97:  function _healthcheck() internal override(EscrowedLine, LineOfCredit) returns(LineLib.STATUS) {
contracts/modules/credit/SpigotedLine.sol:73:    function _init() internal virtual override(LineOfCredit) returns(LineLib.STATUS) {
contracts/modules/credit/SpigotedLine.sol:78:    function unused(address token) external view returns (uint256) {
contracts/modules/credit/SpigotedLine.sol:88:    function _canDeclareInsolvent() internal virtual override returns(bool) {
contracts/modules/credit/SpigotedLine.sol:137:    function useAndRepay(uint256 amount) external whileBorrowing returns(bool) {
contracts/modules/credit/SpigotedLine.sol:213:    function updateOwnerSplit(address revenueContract) external returns (bool) {
contracts/modules/credit/SpigotedLine.sol:244:    function releaseSpigot(address to) external returns (bool) {
contracts/modules/credit/SpigotedLine.sol:255:    function sweep(address to, address token) external nonReentrant returns (uint256) {
contracts/modules/spigot/Spigot.sol:41:    function owner() external view returns (address) {
contracts/modules/spigot/Spigot.sol:45:    function operator() external view returns (address) {
contracts/modules/spigot/Spigot.sol:49:    function treasury() external view returns (address) {
contracts/modules/spigot/Spigot.sol:108:    function operate(address revenueContract, bytes calldata data) external returns (bool) {
contracts/modules/spigot/Spigot.sol:125:    function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
contracts/modules/spigot/Spigot.sol:159:    function updateOwner(address newOwner) external returns (bool) {
contracts/modules/spigot/Spigot.sol:170:    function updateOperator(address newOperator) external returns (bool) {
contracts/modules/spigot/Spigot.sol:181:    function updateTreasury(address newTreasury) external returns (bool) {
contracts/modules/spigot/Spigot.sol:194:     function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {
contracts/modules/spigot/Spigot.sol:206:    function getEscrowed(address token) external view returns (uint256) {
contracts/modules/spigot/Spigot.sol:211:     * @notice - Returns the list of whitelisted functions that an Operator is allowed to perform
contracts/modules/spigot/Spigot.sol:216:    function isWhitelisted(bytes4 func) external view returns(bool) {
contracts/modules/interest-rate/InterestRateCredit.sol:69:    function _calculateInterestOwed(uint256 bpsRate, uint256 balance, uint256 timespan) internal pure returns(uint256) {
contracts/modules/escrow/Escrow.sol:57:    function line() external view override returns(address) {
contracts/modules/escrow/Escrow.sol:65:    function isLiquidatable() external returns(bool) {
contracts/modules/escrow/Escrow.sol:74:    function updateLine(address _line) external returns(bool) {
contracts/modules/escrow/Escrow.sol:100:    function enableCollateral(address token) external returns (bool) {
contracts/modules/escrow/Escrow.sol:126:    function getCollateralRatio() external returns (uint256) {
contracts/modules/escrow/Escrow.sol:135:    function getCollateralValue() external returns (uint256) {
contracts/utils/MutualConsent.sol:38:    function _mutualConsent(address _signerOne, address _signerTwo) internal returns(bool) {
contracts/utils/MutualConsent.sol:65:    function _getNonCaller(address _signerOne, address _signerTwo) internal view returns(address) {
contracts/utils/LineLib.sol:80:    function getBalance(address token) external view returns (uint256) {
contracts/utils/CreditListLib.sol:20:    function removePosition(bytes32[] storage ids, bytes32 id) external returns(bool) {
contracts/utils/CreditListLib.sol:40:    function stepQ(bytes32[] storage ids) external returns(bool) {
contracts/utils/EscrowLib.sol:34:    function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {
contracts/utils/EscrowLib.sol:51:    function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256) {
contracts/utils/EscrowLib.sol:104:    function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {
contracts/utils/EscrowLib.sol:182:    function getCollateralRatio(EscrowState storage self, address oracle) external returns (uint256) {
contracts/utils/EscrowLib.sol:187:    function getCollateralValue(EscrowState storage self, address oracle) external returns (uint256) {
contracts/utils/EscrowLib.sol:210:    function isLiquidatable(EscrowState storage self, address oracle, uint256 minimumCollateralRatio) external returns(bool) {
contracts/utils/EscrowLib.sol:215:    function updateLine(EscrowState storage self, address _line) external returns(bool) {
contracts/utils/SpigotedLineLib.sol:146:    function rollover(address spigot, address newLine) external returns(bool) {
contracts/utils/SpigotedLineLib.sol:151:    function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) {
contracts/utils/SpigotedLineLib.sol:169:    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {
contracts/utils/SpigotedLineLib.sol:194:    function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {
contracts/utils/SpigotedLineLib.sol:217:    function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {
contracts/utils/SpigotLib.sol:61:    function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) {
contracts/utils/SpigotLib.sol:125:    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
contracts/utils/SpigotLib.sol:178:    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
contracts/utils/SpigotLib.sol:187:    function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
contracts/utils/SpigotLib.sol:196:    function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {
contracts/utils/SpigotLib.sol:208:    function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
contracts/utils/SpigotLib.sol:216:    function getEscrowed(SpigotState storage self, address token) external view returns (uint256) {
contracts/utils/SpigotLib.sol:221:    function isWhitelisted(SpigotState storage self, bytes4 func) external view returns(bool) {
```
**Mitigation**: Consider refactoring functions to standardise on one return pattern, preferably explicit returns.



## Use `allowlist/denylist` rather than `blacklist/whitelist`
```solidity
contracts/modules/credit/SpigotedLine.sol:234:    /// see ISpigotedLine.updateWhitelist
contracts/modules/credit/SpigotedLine.sol:235:    function updateWhitelist(bytes4 func, bool allowed)
contracts/modules/credit/SpigotedLine.sol:240:        return spigot.updateWhitelistedFunction(func, allowed);
contracts/modules/spigot/Spigot.sol:28:     * @param _operator - An active address for non-Owner that can execute whitelisted functions to manage and maintain product operations
contracts/modules/spigot/Spigot.sol:101:     * @notice - Allows Operator to call whitelisted functions on revenue contracts to maintain their product
contracts/modules/spigot/Spigot.sol:187:     * @notice - Allows Owner to whitelist function methods across all revenue contracts for Operator to call.
contracts/modules/spigot/Spigot.sol:188:     *           Can whitelist "transfer ownership" functions on revenue contracts
contracts/modules/spigot/Spigot.sol:191:     * @param func - smart contract function signature to whitelist
contracts/modules/spigot/Spigot.sol:194:     function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {
contracts/modules/spigot/Spigot.sol:195:        return state.updateWhitelistedFunction(func, allowed);
contracts/modules/spigot/Spigot.sol:211:     * @notice - Returns the list of whitelisted functions that an Operator is allowed to perform
contracts/modules/spigot/Spigot.sol:213:     * @param func Function to check on whitelist 
contracts/modules/spigot/Spigot.sol:216:    function isWhitelisted(bytes4 func) external view returns(bool) {
contracts/modules/spigot/Spigot.sol:217:      return state.isWhitelisted(func);
contracts/modules/escrow/Escrow.sol:96:     * @dev - whitelisting protects against malicious 4626 tokens and DoS attacks
contracts/utils/SpigotLib.sol:14:    mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
contracts/utils/SpigotLib.sol:64:        // extract function signature from tx data and check whitelist
contracts/utils/SpigotLib.sol:67:        if(!self.whitelistedFunctions[func]) { revert BadFunction(); }
contracts/utils/SpigotLib.sol:69:        // cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case
contracts/utils/SpigotLib.sol:207:    /** see Spigot.updateWhitelistedFunction*/
contracts/utils/SpigotLib.sol:208:    function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
contracts/utils/SpigotLib.sol:210:        self.whitelistedFunctions[func] = allowed;
contracts/utils/SpigotLib.sol:211:        emit UpdateWhitelistFunction(func, allowed);
contracts/utils/SpigotLib.sol:220:    /** see Spigot.isWhitelisted*/
contracts/utils/SpigotLib.sol:221:    function isWhitelisted(SpigotState storage self, bytes4 func) external view returns(bool) {
contracts/utils/SpigotLib.sol:222:      return self.whitelistedFunctions[func];
contracts/utils/SpigotLib.sol:248:    event UpdateWhitelistFunction(bytes4 indexed func, bool indexed allowed);
```

