# Report
## Non-Critical Issues ##

### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return id;``` [L453](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L453) 
1. ```return state.claimRevenue(revenueContract, token, data);``` [L74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L74) 
1. ```return state.claimEscrow(token);``` [L90](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L90) 
1. ```return (c, principal, interest);``` [L97](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L97) 
1. ```return credit;``` [L160](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L160) 
1. ```return claimed;``` [L57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L57) 
1. ```return claimed;``` [L101](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L101) 
1. ```return claimed;``` [L121](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L121) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Wrong order of functions
**Context:**

1. [modifier whileActive](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L78) (modifier definition can not go after internal function)
1. [SpigotedLine.unused](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L78) (external function can not go after internal function)
1. [SpigotedLine.receive](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272) (receive function must be after constructor and before external functions)
1. [SecuredLine.rollover](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L48) (external function can not go after internal function)
1. [Spigot.claimRevenue](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L70) (external function can not go after external view function)
1. [Escrow.isLiquidatable](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L65) (external function can not go after external view function)
1. [EscrowLib.addCollateral](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L87) (external function can not go after public function)
1. [SpigotLib.operate](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L61) (external function can not go after public function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-3]: Require() statements should have descriptive reason string
**Context:**

1. ```require(uint(status) >= uint( LineLib.STATUS.ACTIVE));``` [L112](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112) 
1. ```require(interestRate.setRate(id, drate, frate));``` [L241](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L241) 
1. ```require(interestRate.setRate(id, drate, frate));``` [L259](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L259) 
1. ```require(amount <= credit.principal + credit.interestAccrued);``` [L326](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326) 
1. ```require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);``` [L62](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62) 
1. ```require(amount <= unusedTokens[credit.token]);``` [L143](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143) 
1. ```require(msg.sender == borrower);``` [L160](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160) 
1. ```require(msg.sender == arbiter);``` [L239](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239) 
1. ```require(escrow_.liquidate(amount, targetToken, to));``` [L64](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L64) 
1. ```require(escrow.updateLine(newLine));``` [L90](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L90) 
1. ```require(amount > 0);``` [L91](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91) 
1. ```require(msg.sender == ILineOfCredit(self.line).arbiter());``` [L105](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105) 
1. ```require(amount > 0);``` [L161](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161) 
1. ```require(amount > 0);``` [L198](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198) 
1. ```require(msg.sender == self.line);``` [L216](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216) 
1. ```require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));``` [L96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96) 
1. ```require(revenueContract != address(this));``` [L128](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L128) 
1. ```require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));``` [L130](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L130) 
1. ```require(success);``` [L155](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L155) 
1. ```require(newOwner != address(0));``` [L180](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180) 
1. ```require(newOperator != address(0));``` [L189](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189) 
1. ```require(newTreasury != address(0));``` [L201](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201) 
1. ```require(ISpigot(spigot).updateOwner(newLine));``` [L147](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L147) 

### [N-4]: NatSpec is missing
**Context:**

1. ```function init() external virtual returns(LineLib.STATUS) {``` [L64](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L64) 
1. ```function _init() internal virtual returns(LineLib.STATUS) {``` [L69](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L69) 
1. ```modifier whileActive() {``` [L78](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L78) 
1. ```modifier whileBorrowing() {``` [L83](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L83) 
1. ```modifier onlyBorrower() {``` [L88](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L88) 
1. ```function counts() external view returns (uint256, uint256) {``` [L117](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L117) 
1. ```function _healthcheck() internal virtual returns (LineLib.STATUS) {``` [L121](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L121) 
1. ```function declareInsolvent() external whileBorrowing returns(bool) {``` [L143](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L143) 
1. ```function _canDeclareInsolvent() internal virtual returns(bool) {``` [L157](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L157) 
1. ```function updateOutstandingDebt() external override returns (uint256, uint256) {``` [L163](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L163) 
1. ```function _updateOutstandingDebt()``` [L167](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L167) 
1. ```function accrueInterest() external override returns(bool) {``` [L200](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L200) 
1. ```function addCredit(``` [L223](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L223) 
1. ```function setRates(``` [L247](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L247) 
1. ```function increaseCredit(bytes32 id, uint256 amount)``` [L265](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L265) 
1. ```function depositAndClose()``` [L292](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L292) 
1. ```function depositAndRepay(uint256 amount)``` [L315](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L315) 
1. ```function borrow(bytes32 id, uint256 amount)``` [L340](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L340) 
1. ```function withdraw(bytes32 id, uint256 amount)``` [L370](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L370) 
1. ```function close(bytes32 id) external payable override returns (bool) {``` [L388](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L388) 
1. ```function unused(address token) external view returns (uint256) {``` [L78](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L78) 
1. ```function claimAndRepay(address claimToken, bytes calldata zeroExTradeData)``` [L93](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L93) 
1. ```function useAndRepay(uint256 amount) external whileBorrowing returns(bool) {``` [L137](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L137) 
1. ```function claimAndTrade(address claimToken, bytes calldata zeroExTradeData)``` [L154](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L154) 
1. ```function updateOwnerSplit(address revenueContract) external returns (bool) {``` [L213](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L213) 
1. ```function addSpigot(``` [L223](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L223) 
1. ```function updateWhitelist(bytes4 func, bool allowed)``` [L235](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L235) 
1. ```function releaseSpigot(address to) external returns (bool) {``` [L244](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L244) 
1. ```function sweep(address to, address token) external nonReentrant returns (uint256) {``` [L255](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L255) 
1. ```constructor(``` [L13](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L13) 
1. ```function rollover(address newLine)``` [L48](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L48) 
1. ```function _healthcheck() internal override(EscrowedLine, LineOfCredit) returns(LineLib.STATUS) {``` [L97](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L97) 
1. ```constructor(address _escrow) {``` [L16](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L16) 
1. ```function owner() external view returns (address) {``` [L41](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L41) 
1. ```function operator() external view returns (address) {``` [L45](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L45) 
1. ```function treasury() external view returns (address) {``` [L49](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L49) 
1. ```function updateOwnerSplit(address revenueContract, uint8 ownerSplit)``` [L146](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L146) 
1. ```function getSetting(address revenueContract)``` [L220](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L220) 
1. ```constructor(address _registry) {``` [L15](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L15) 
1. ```function accrueInterest(``` [L34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L34) 
1. ```function _accrueInterest(``` [L42](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L42) 
1. ```function setRate(``` [L74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L74) 
1. ```constructor(``` [L20](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L20) 
1. ```function deployEscrow(``` [L42](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L42) 
1. ```function deploySpigot(``` [L51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L51) 
1. ```function deploySecuredLine(address borrower, uint256 ttl)``` [L59](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L59) 
1. ```function deploySecuredLineWithConfig(CoreLineParams calldata coreParams)``` [L87](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L87) 
1. ```event AddCredit(``` [L16](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L16) 
1. ```function getOutstandingDebt(``` [L73](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L73) 
1. ```function addCollateral(EscrowState storage self, address oracle, uint256 amount, address token)``` [L87](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L87) 
1. ```function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {``` [L104](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L104) 
1. ```function releaseCollateral(``` [L152](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L152) 
1. ```function getCollateralRatio(EscrowState storage self, address oracle) external returns (uint256) {``` [L182](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L182) 
1. ```function getCollateralValue(EscrowState storage self, address oracle) external returns (uint256) {``` [L187](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L187) 
1. ```function liquidate(``` [L192](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L192) 
1. ```function isLiquidatable(EscrowState storage self, address oracle, uint256 minimumCollateralRatio) external returns(bool) {``` [L210](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L210) 
1. ```function updateLine(EscrowState storage self, address _line) external returns(bool) {``` [L215](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L215) 
1. ```function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)``` [L29](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L29) 
1. ```function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) {``` [L61](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L61) 
1. ```function claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)``` [L83](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L83) 
1. ```function claimEscrow(SpigotState storage self, address token)``` [L105](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L105) 
1. ```function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125) 
1. ```function removeSpigot(SpigotState storage self, address revenueContract)``` [L143](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L143) 
1. ```function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)``` [L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L164) 
1. ```function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {``` [L178](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178) 
1. ```function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {``` [L187](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L187) 
1. ```function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {``` [L196](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L196) 
1. ```function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {``` [L208](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L208) 
1. ```function getEscrowed(SpigotState storage self, address token) external view returns (uint256) {``` [L216](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L216) 
1. ```function isWhitelisted(SpigotState storage self, bytes4 func) external view returns(bool) {``` [L221](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L221) 
1. ```function getSetting(SpigotState storage self, address revenueContract)``` [L227](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L227) 
1. ```function trade(``` [L120](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L120) 
1. ```function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) {``` [L151](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L151) 
1. ```function _mutualConsent(address _signerOne, address _signerTwo) internal returns(bool) {``` [L38](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L38) 
1. ```function _getNonCaller(address _signerOne, address _signerTwo) internal view returns(address) {``` [L65](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L65) 
1. ```event DeployedSecuredLine(``` [L8](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L8) 
1. ```event DeployedSpigot(``` [L16](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L16) 
1. ```event DeployedEscrow(``` [L23](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L23) 
1. ```function transferModulesToLine(address line, address spigot, address escrow) external {``` [L57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L57) 
1. ```function deploySecuredLine(``` [L77](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L77) 

### [N-5]: Typos
**Context:**

1. ```// Line Financials aggregated accross all existing  Credit``` [L37](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L37) (Change ***accross*** to ***across***)
1. ```* @param arbiter_    - A neutral party with some special priviliges on behalf of Borrower and Lender.``` [L45](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L45) (Change ***priviliges*** to ***privileges***)
1. ```* @dev - updates `status` variable in storage if current status is diferent from existing status``` [L107](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L107) (Change ***diferent*** to ***different***)
1. ```@notice - accrues token demoninated interest on a lender's position.``` [L213](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L213) (Change ***demoninated*** to ***denominated***)
1. ```// ensure that borrowing doesnt cause Line to be LIQUIDATABLE``` [L355](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L355) (Change ***doesnt*** to ***doesn't***)
1. ```// ensure all money owed is accounted for. Accrue facility fee since prinicpal was paid off``` [L395](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L395) (Change ***prinicpal*** to ***principal***)
1. ```//  Internal  funcs //``` [L412](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L412) (Change ***funcs*** to ***functions***)
1. ```* @notice - updates `status` variable in storage if current status is diferent from existing status.``` [L416](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L416) (Change ***diferent*** to ***different***)
1. ```* @notice - Insert `p` into the next availble FIFO position in the repayment queue``` [L510](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L510) (Change ***availble*** to ***available***)
1. ```* @param arbiter_ - neutral party with some special priviliges on behalf of borrower and lender``` [L46](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L46) (Change ***priviliges*** to ***privileges***)
1. ```* @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent``` [L84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84) (Change ***itselgf*** to ***itself***)
1. ```* @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent``` [L84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84) (Change ***transfered*** to ***transferred***)
1. ```spigot.claimEscrow(claimToken) :  // same asset. dont trade``` [L107](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L107) (Change ***dont*** to ***don't***)
1. ```spigot.claimEscrow(claimToken) : // same asset. dont trade``` [L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L164) (Change ***dont*** to ***don't***)
1. ```* @dev     - priviliged internal function``` [L180](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L180) (Change ***priviliged*** to ***privileged***)
1. ```// we dont use revenue after this so can store now``` [L204](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L204) (Change ***dont*** to ***don't***)
1. ```// we dont check borrower is same on both lines because borrower might want new address managing new line``` [L58](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L58) (Change ***dont*** to ***don't***)
1. ```* @return isInsolvent - if the entire Line including all collateral sources is fuly insolvent.``` [L110](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L110) (Change ***fuly*** to ***fully***)
1. ```* @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment``` [L84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84) (Change ***swithc*** to ***switch***)
1. ```* @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment``` [L84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84) (Change ***repyment*** to ***repayment***)
1. ```// #####   Claimoooor   #####``` [L54](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L54) (Change ***Claimoooor*** to ***Claimor***)
1. ```// #####  OPERATOOOR    #####``` [L96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L96) (Change ***OPERATOOOR*** to ***OPERATOR***)
1. ```// #####  OPERATOOOR    #####``` [L97](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L97) (Change ***OPERATOOOR*** to ***OPERATOR***)
1. ```// #####  Maintainooor  #####``` [L115](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L115) (Change to ***Maintainor***)
1. ```* @dev - revenuContract's transfer func MUST only accept one paramteter which is the new owner.``` [L133](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L133) (Change ***paramteter*** to ***parameter***)
1. ```* @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever``` [L71](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71) (Change ***transfered*** to ***transferred***)
1. ```* @notice - allows  the lines arbiter to  enable thdeposits of an asset``` [L94](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L94) (Change ***thdeposits*** to ***the deposits***)
1. ```*        - gives  better risk segmentation forlenders``` [L95](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L95) (Change ***forlenders*** to ***for lenders***)
1. ```* @notice Interest rate / acrrued interest calculation contract for Line of Credit contracts``` [L17](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L17) (Change ***acrrued*** to ***accrued***)
1. ```* @param oracle - interset rate contract used by line that will calculate interest owed``` [L123](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L123) (Change ***interset*** to ***interest***)
1. ```* @param credit - The lender position that is being bwithdrawn from``` [L200](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L200) (Change ***bwithdrawn*** to ***withdrawn***)
1. ```// emit events before seeting to 0``` [L221](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221) (Change ***seeting*** to ***setting***)
1. ```* @param interest - interset rate contract used by line that will calculate interest owed``` [L237](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L237) (Change ***interset*** to ***interest***)
1. ```// get token demoninated interest accrued``` [L250](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L250) (Change ***demoninated*** to ***denominated***)
1. ```// cap so uint doesnt overflow in split calculations.``` [L53](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L53) (Change ***doesnt*** to ***doesn't***)
1. ```* @dev                 priviliged internal function``` [L44](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L44) (Change ***priviliged*** to ***privileged***)
1. ```* @param spigot        - The Spigot to claim from. Must be owned by adddress(this)``` [L48](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L48) (Change ***adddress(this)*** to ***address(this)***)
1. ```* @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either``` [L188](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L188) (Change ***revenuw*** to ***revenue***)

### [N-6]: TODO
**Context:**

1. ```// TODO: test``` [L140](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140) 
1. ```// TODO: test``` [L145](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145) 

### [N-7]: Natspec is incomplete
**Context:**

1. ```function _updateStatus(LineLib.STATUS status_) internal returns(LineLib.STATUS) {``` [L421](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L421) (param tag status_ is missing)
1. ```function _createCredit(``` [L435](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L435) (return tag id is missing)
1. ```function _repay(Credit memory credit, bytes32 id, uint256 amount)``` [L465](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L465) (param tag credit is missing)
1. ```function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {``` [L483](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483) (param tags credit and id are missing)
1. ```function liquidate(``` [L80](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L80) (return tag is missing)
1. ```function _init() internal virtual returns(LineLib.STATUS) {``` [L25](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L25) (return tag is missing)
1. ```function _healthcheck() virtual internal returns(LineLib.STATUS) {``` [L34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L34) (return tag is missing)
1. ```function _rollover(address newLine) internal virtual returns(bool) {``` [L89](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L89) (param tag newLine is missing)
1. ```function claimRevenue(address revenueContract, address token, bytes calldata data)``` [L70](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L70) (param tag token is missing)
1. ```function operate(address revenueContract, bytes calldata data) external returns (bool) {``` [L108](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L108) (return tag is missing)
1. ```function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125) (return tag is missing)
1. ```function removeSpigot(address revenueContract)``` [L137](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L137) (return tag is missing)
1. ```function updateOwner(address newOwner) external returns (bool) {``` [L159](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L159) (return tag is missing)
1. ```function updateOperator(address newOperator) external returns (bool) {``` [L170](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L170) (return tag is missing)
1. ```function updateTreasury(address newTreasury) external returns (bool) {``` [L181](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L181) (return tag is missing)
1. ```function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {``` [L194](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L194) (return tag is missing)
1. ```function getEscrowed(address token) external view returns (uint256) {``` [L206](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L206) (return tag is missing)
1. ```function isWhitelisted(bytes4 func) external view returns(bool) {``` [L216](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L216) (return tag is missing)
1. ```function line() external view override returns(address) {``` [L57](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L57) (return tag is missing)
1. ```function updateLine(address _line) external returns(bool) {``` [L74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L74) (param tag _line is missing)
1. ```function enableCollateral(address token) external returns (bool) {``` [L100](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L100) (return tag is missing)
1. ```function getLatestAnswer(address token) external returns (int) {``` [L22](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L22) (param tag token is missing)
1. ```function deploySecuredLineWithModules(``` [L135](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L135) (params tag coreParams, mSpigot, mEscrow are missing)
1. ```function deploySecuredLineWithModules(``` [L135](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L135) (return tag line is missing)
1. ```function create(``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L125) (param tags are missing)
1. ```function create(``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L125) (return tag is missing)
1. ```function repay(``` [L168](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L168) (param tags are missing)
1. ```function withdraw(``` [L202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L202) (param tags are missing)
1. ```function accrue(``` [L239](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L239) (param tags are missing)
1. ```function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {``` [L34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L34) (param self is missing)
1. ```function rollover(address spigot, address newLine) external returns(bool) {``` [L146](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L146) (param tags are missing)
1. ```function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {``` [L169](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L169) (param tags spigot, status, defaultSplit are missing)
1. ```function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {``` [L194](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L194) (param tags are missing)
1. ```function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {``` [L217](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L217) (param tags are missing)
1. ```modifier mutualConsent(address _signerOne, address _signerTwo) {``` [L31](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L31) (param tags are missing)
1. ```function rolloverSecuredLine(``` [L41](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L41) (param tag oracle is missing)

### [N-8]: Line is too long
**Context:**

1. ```mapping(bytes32 => Credit) public credits; // id -> Reference ID for a credit line provided by a single Lender for a given token on a Line of Credit``` [L35](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L35) 
1. ```* @dev               - A Borrower and a first Lender agree on terms. Then the Borrower deploys the contract using the constructor below.``` [L42](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L42)
1. ```*                      Later, both Lender and Borrower must call _mutualConsent() during addCredit() to actually enable funds to be deposited.``` [L43](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L43)
1. ```* @param ttl_        - The time to live for all credit lines for the Line of Credit facility (sets the maturity/term of the Line of Credit)``` [L47](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L47)
1. ```deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility``` [L58](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L58)
1. ```/// @notice exchange aggregator (mainly 0x router) to trade revenue tokens from a Spigot for credit tokens owed to lenders``` [L28](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L28) 
1. ```* @notice - excess unsold revenue claimed from Spigot to be sold later or excess credit tokens bought from revenue but not yet used to repay debt``` [L36](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L36) 
1. ```*         - needed because the Line of Credit might have the same token being lent/borrower as being bought/sold so need to separate accounting.``` [L37](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L37) 
1. ```* @param swapTarget_ - 0x protocol exchange address to send calldata for trades to exchange revenue tokens for credit tokens``` [L49](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L49) 
1. ```* @param ttl_ - time to live for line of credit contract across all lenders set at deployment in order to set the term/expiry date``` [L50](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L50) 
1. ```* @param defaultRevenueSplit_ - The % of Revenue Tokens that the Spigot escrows for debt repayment if the Line is healthy.``` [L51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L51) 
1. ```* @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent``` [L84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84) 
1. ```* @param targetToken - The credit token that needs to be bought in order to pat down debt. Always `credits[ids[0]].token```` [L182](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L182)
1. ```*          - current implementation just sends "liquidated" tokens to Arbiter to sell off how the deem fit and then manually repay with DepositAndRepay``` [L72](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L72) 
1. ```* @notice  - a contract allowing the revenue stream of a smart contract to be split between two parties, Owner and Treasury``` [L12](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L12) 
1. ```* @param _owner    - An address that controls the Spigot and owns rights to some or all tokens earned by owned revenue contracts``` [L26](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L26) 
1. ```* @param _treasury - A non-active address for non-Owner that receives revenue tokens that aren't allocated and escrowed for the Owner``` [L27](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L27)
1. ```* @param _operator - An active address for non-Owner that can execute whitelisted functions to manage and maintain product operations``` [L28](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L28) 
1. ```* @notice - Claims revenue tokens from the Spigot (push and pull payments) and escrows them for the Owner withdraw later.``` [L59](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L59) 
1. ```* @return claimed -  The amount of revenue tokens claimed from revenueContract and split between `owner` and `treasury```` [L68](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L68) 
1. ```* @notice - uses predefined function in revenueContract settings to transfer complete control and ownership from this Spigot to the Operator``` [L131](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L131) 
1. ```* @notice - Ownable contract that allows someone to deposit ERC20 and ERC4626 tokens as collateral to back a Line of Credit``` [L17](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L17)
1. ```* @param _minimumCollateralRatio - In bps, 3 decimals. Cratio threshold where liquidations begin. see Escrow.isLiquidatable()``` [L36](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L36) 
1. ```* @param _line      - Initial owner of Escrow contract. May be non-Line contract at construction before transferring to a Line.``` [L38](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L38) 
1. ```* @param _borrower  - borrower on the _line contract. Cannot pull from _line because _line might not be a Line at construction.``` [L40](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L40)
1. ```* @notice - Checks Line's outstanding debt value and current Escrow collateral value to compute collateral ratio and checks that against minimum.``` [L62](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L62)
1. ```* @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever``` [L71](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71) 
1. ```* @notice  - simple contract that wraps Chainlink's Feed Registry to get asset prices for any tokens without needing to know the specific oracle address``` [L10](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L10)
1. ```@notice sets up new line based of config of old line. Old line does not need to have REPAID status for this call to succeed.``` [L171](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L171)
1. ```/// @notice After accrueInterest runs, emits the amount of interest added to a Borrower's outstanding balance of interest due``` [L31](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L31) 
1. ```(N.B. results in an increase in interestRepaid, i.e. interest not yet withdrawn by a Lender). There is no corresponding function``` [L43](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L43)
1. ```* @dev          - Creates a deterministic hash id for a credit line provided by a single Lender for a given token on a Line of Credit facility``` [L55](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L55) 
1. ```* @notice         - Calculates value of tokens.  Used for calculating the USD value of principal and of interest during getOutstandingDebt()``` [L101](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L101)
1. ```* @dev assumes that `id` of a single credit line within the Line of Credit facility (same lender/token) is stored only once in the `positions` array``` [L12](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L12)
1. ```* This means cleanup on _close() and checks on addCredit() are CRITICAL. If `id` is duplicated then the position can't be closed``` [L14](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L14)
1. ```* @return newPositions - all active credit lines on the Line of Credit facility after the `id` has been removed [Bob - consider renaming to newIds``` [L17](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L17)
1. ```function isLiquidatable(EscrowState storage self, address oracle, uint256 minimumCollateralRatio) external returns(bool) {``` [L210](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L210)
1. ```// Configurations for revenue contracts related to the split of revenue, access control to claiming revenue tokens and transfer of Spigot ownership``` [L15](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L15)
1. ```// cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case``` [L69](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L69)
1. ```function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {``` [L125](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125) 
1. ```* @notice              Allows revenue tokens in 'escrowed' to be traded for credit tokens that aren't yet used to repay debt.``` [L39](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L39) 
1. ```The newly exchanged credit tokens are held in 'unusedTokens' ready for a Lender to withdraw using useAndRepay``` [L40](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L40) 
1. ```This feature allows a Borrower to take advantage of an increase in the value of the revenue token compared``` [L41](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L41) 
1. ```to the credit token and to in effect use less revenue tokens to be later used to repay the same amount of debt.``` [L42](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L42) 
1. ```* @param targetToken   - The credit token that needs to be bought in order to pat down debt. Always `credits[ids[0]].token```` [L46](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L46) 
1. ```* @notice Changes the revenue split between a Borrower's treasury and the LineOfCredit based on line health, runs with updateOwnerSplit()``` [L164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L164)
1. ```function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {``` [L169](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L169) 
1. ```function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {``` [L194](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L194) 
1. ```* @notice -  Sends any remaining tokens (revenue or credit tokens) in the Spigot to the Borrower after the loan has been repaid.``` [L212](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L212)
1. ```-  In case of a Borrower default (loan status = liquidatable), this is a fallback mechanism to withdraw all the tokens and send them to the Arbiter``` [L213](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L213) 
1. ```function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {``` [L217](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L217) 
1. ```@notice sets up new line based of config of old line. Old line does not need to have REPAID status for this call to succeed.``` [L34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L34) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
