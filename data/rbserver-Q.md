## [L-01] POSSIBLE DOS WHEN CALLING `LineLib.sendOutTokenOrETH` FUNCTION FOR ETH
Calling the following `LineLib.sendOutTokenOrETH` function for ETH executes `payable(receiver).transfer(amount)`. Since the `transfer` function only forwards a fixed 2300 gas stipend, if the `to` address corresponds to a contract that has gas-intensive logics, which need more than 2300 gas, in its `receive` function, executing `payable(receiver).transfer(amount)` will revert. Functions like `LineOfCredit.withdraw` and `LineOfCredit._close` would call the `LineLib.sendOutTokenOrETH` function with the `receiver` input being `credit.lender`, which was set when the credit was created and cannot be changed; if `credit.lender` happens to be a contract that has gas-intensive logics, which need more than 2300 gas, in its `receive` function, calling the `LineLib.sendOutTokenOrETH` function will revert and DOS will occur. As a result, the `receiver` address, such as `credit.lender`, for the `LineLib.sendOutTokenOrETH` function cannot receive the specified `amount` of the corresponding `token`.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L34-L51
```solidity
    function sendOutTokenOrETH(
      address token,
      address receiver,
      uint256 amount
    )
      external
      returns (bool)
    {
        if(token == address(0)) { revert TransferFailed(); }
        
        // both branches revert if call failed
        if(token!= Denominations.ETH) { // ERC20
            IERC20(token).safeTransfer(receiver, amount);
        } else { // ETH
            payable(receiver).transfer(amount);
        }
        return true;
    }
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L370-L385
```solidity
    function withdraw(bytes32 id, uint256 amount)
        external
        override
        returns (bool)
    {
        Credit memory credit = credits[id];

        ...

        LineLib.sendOutTokenOrETH(credit.token, credit.lender, amount);

        return true;
    }
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483-L507
```solidity
    function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
        ...
        if (credit.deposit + credit.interestRepaid > 0) {
            LineLib.sendOutTokenOrETH(
                credit.token,
                credit.lender,
                credit.deposit + credit.interestRepaid
            );
        }

        ...
    }
```

### Recommended Mitigation Step
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L47-L49 can be updated to the following code.
```solidity
        } else { // ETH
            (bool success, ) = receiver.call{value: amount}("");
            require(success, "ETH transfer failed");
        }
```

## [L-02] MISSING TWO-STEP PROCEDURES FOR SETTING escrow `line`, spigot `owner`, and spigot `operator`
The following `Escrow.updateLine`, `Spigot.updateOwner`, and `Spigot.updateOperator` functions can be called to set the escrow `line` and the spigot `owner` and `operator`. Because these functions do not include a two-step procedure, it is possible to assign these roles to wrong addresses. When this occurs, the functions that are only callable by these roles can become no-ops or be maliciously called if the incorrectly assigned addresses happen to be malicious. To reduce the potential attack surface, please consider implementing a two-step procedure for assigning each of these roles.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L74-L76
```solidity
    function updateLine(address _line) external returns(bool) {
      return state.updateLine(_line);
    }
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L159-L161
```solidity
    function updateOwner(address newOwner) external returns (bool) {
        return state.updateOwner(newOwner);
    }
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L170-L172
```solidity
    function updateOperator(address newOperator) external returns (bool) {
        return state.updateOperator(newOperator);
    }
```

## [L-03] MISSING `address(0)` CHECKS FOR CRITICAL ADDRESS INPUTS
To prevent unintended behaviors, critical addresses inputs should be checked against `address(0)`.

Please consider checking `_escrow` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L16-L18
```solidity
  constructor(address _escrow) {
    escrow = IEscrow(_escrow);
  }
```

Please consider checking `oracle_`, `arbiter_`, and `borrower_` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L49-L62
```solidity
    constructor(
        address oracle_,
        address arbiter_,
        address borrower_,
        uint256 ttl_
    ) {
        oracle = IOracle(oracle_);
        arbiter = arbiter_;
        borrower = borrower_;
        deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility
        interestRate = new InterestRateCredit();

        emit DeployLine(oracle_, arbiter_, borrower_);
    }
```

Please consider checking `spigot_` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L53-L67
```solidity
    constructor(
        address oracle_,
        address arbiter_,
        address borrower_,
        address spigot_,
        address payable swapTarget_,
        uint256 ttl_,
        uint8 defaultRevenueSplit_
    ) LineOfCredit(oracle_, arbiter_, borrower_, ttl_) {
        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

        spigot = ISpigot(spigot_);
        defaultRevenueSplit = defaultRevenueSplit_;
        swapTarget = swapTarget_;
    }
```

Please consider checking `_oracle`,  `_line`, and `_borrower` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L42-L52
```solidity
    constructor(
        uint32 _minimumCollateralRatio,
        address _oracle,
        address _line,
        address _borrower
    ) {
        minimumCollateralRatio = _minimumCollateralRatio;
        oracle = _oracle;
        borrower = _borrower;
        state.line = _line;
    }
```

Please consider checking `_registry` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L15-L17
```solidity
    constructor(address _registry) {
        registry = FeedRegistryInterface(_registry);
    }
```

Please consider checking `_owner`, `_treasury`, and `_operator` in the following constructor.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L31-L39
```solidity
    constructor (
        address _owner,
        address _treasury,
        address _operator
    ) {
        state.owner = _owner;
        state.operator = _operator;
        state.treasury = _treasury;
    }
```

## [L-04] UNRESOLVED TODO/QUESTION COMMENT
A comment regarding todo/question indicates that there is an unresolved action item for implementation, which needs to be addressed before the protocol deployment. Please review the todo/question comment in the following code.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L105-L122
```solidity
    function claimEscrow(SpigotState storage self, address token)
        external
        returns (uint256 claimed) 
    {
        ...

        self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?

        ...
    }
```

## [N-01] COMMENTED OUT CODE CAN BE REMOVED
The following line of code is commented out. To improve readability and maintainability, please consider removing it.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L10
```solidity
// import { SecuredLine } from "./SecuredLine.sol";
```

## [N-02] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of magic numbers. Please consider replacing the following magic numbers, such as `18`, with constants.

```solidity
contracts\utils\CreditLib.sol
  140: decimals = 18;  

contracts\utils\EscrowLib.sol
  113: deposit.assetDecimals = 18; 
  137: deposit.assetDecimals = 18; 

contracts\utils\SpigotLib.sol
  90: uint256 escrowedAmount = claimed * self.settings[revenueContract].ownerSplit / 100; 
```

## [N-03] MISSING REASON STRINGS IN `require` STATEMENTS
When the reason strings are missing in the `require` statements, it is unclear about why certain conditions revert. Please add descriptive reason strings for the following `require` statements.

```solidity
contracts\modules\credit\EscrowedLine.sol
  64: require(escrow_.liquidate(amount, targetToken, to));
  90: require(escrow.updateLine(newLine));  

contracts\modules\credit\LineOfCredit.sol
  112: require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
  241: require(interestRate.setRate(id, drate, frate));    
  259: require(interestRate.setRate(id, drate, frate));    
  326: require(amount <= credit.principal + credit.interestAccrued);   

contracts\modules\credit\SpigotedLine.sol
  62: require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT); 
  143: require(amount <= unusedTokens[credit.token]);    
  160: require(msg.sender == borrower);    
  239: require(msg.sender == arbiter); 

contracts\utils\EscrowLib.sol
  91: require(amount > 0);    
  105: require(msg.sender == ILineOfCredit(self.line).arbiter());  
  161: require(amount > 0);    
  198: require(amount > 0);    
  216: require(msg.sender == self.line); 

contracts\utils\SpigotedLineLib.sol
  147: require(ISpigot(spigot).updateOwner(newLine));  

contracts\utils\SpigotLib.sol
  96: require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
  128: require(revenueContract != address(this));  
  130: require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));  
  155: require(success);   
  180: require(newOwner != address(0));    
  189: require(newOperator != address(0));    
  201: require(newTreasury != address(0));    
```

## [N-04] SPELLING TYPOS IN COMMENTS
`swithc` can be changed to `switch` and `priviliegad` can be changed to `privileged` in the following comments.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84-L85
```solidity
   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
   *(@dev priviliegad internal function.
```

`itselgf` can be changed to `itself` and `priviliegad` can be changed to `privileged` in the following comments.
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84-L85
```solidity
      * @notice requires Spigot contract itselgf to be transfered to Arbiter and sold off to a 3rd party before declaring insolvent
      *(@dev priviliegad internal function.
```

## [N-05] MISSING NATSPEC COMMENTS
NatSpec comments provide rich code documentation. NatSpec comments are missing for the following functions. Please consider adding them.

```solidity
contracts\modules\credit\LineOfCredit.sol
  64: function init() external virtual returns(LineLib.STATUS) {
  69: function _init() internal virtual returns(LineLib.STATUS) {  
  121: function _healthcheck() internal virtual returns (LineLib.STATUS) { 
  157: function _canDeclareInsolvent() internal virtual returns(bool) {    
  167: function _updateOutstandingDebt()    

contracts\modules\credit\SecuredLine.sol
  97: function _healthcheck() internal override(EscrowedLine, LineOfCredit) returns(LineLib.STATUS) { 

contracts\modules\credit\SpigotedLine.sol
  78: function unused(address token) external view returns (uint256) {    

contracts\modules\interest-rate\InterestRateCredit.sol
  42: function _accrueInterest(   

contracts\modules\spigot\Spigot.sol
  41: function owner() external view returns (address) {  
  45: function operator() external view returns (address) {  
  49: function treasury() external view returns (address) {  
  146: function updateOwnerSplit(address revenueContract, uint8 ownerSplit)    
  220: function getSetting(address revenueContract)    

contracts\utils\CreditLib.sol
  73: function getOutstandingDebt(  

contracts\utils\MutualConsent.sol
  38: function _mutualConsent(address _signerOne, address _signerTwo) internal returns(bool) {    
  65: function _getNonCaller(address _signerOne, address _signerTwo) internal view returns(address) {    

contracts\utils\SpigotedLineLib.sol
  120: function trade( 
  151: function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) {  

contracts\utils\SpigotLib.sol
  29: function _claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)   
```

## [N-06] INCOMPLETE NATSPEC COMMENTS
NatSpec comments provide rich code documentation. @param and/or @return comments are missing for the following functions. Please consider completing NatSpec comments for them.

```solidity
contracts\modules\credit\EscrowedLine.sol
  89: function _rollover(address newLine) internal virtual returns(bool) {  

contracts\modules\credit\LineOfCredit.sol
  421: function _updateStatus(LineLib.STATUS status_) internal returns(LineLib.STATUS) {   
  435: function _createCredit( 
  465: function _repay(Credit memory credit, bytes32 id, uint256 amount)   
  483: function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) { 

contracts\modules\credit\SecuredLine.sol
  80: function liquidate( 

contracts\modules\credit\SpigotedLine.sol
  73: function _init() internal virtual override(LineOfCredit) returns(LineLib.STATUS) {  

contracts\modules\escrow\Escrow.sol
  57: function line() external view override returns(address) {   
  74: function updateLine(address _line) external returns(bool) {
  100: function enableCollateral(address token) external returns (bool) {   

contracts\modules\spigot\Spigot.sol
  70: function claimRevenue(address revenueContract, address token, bytes calldata data)  
  108: function operate(address revenueContract, bytes calldata data) external returns (bool) {    
  125: function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {    
  137: function removeSpigot(address revenueContract)    
  159: function updateOwner(address newOwner) external returns (bool) {
  170: function updateOperator(address newOperator) external returns (bool) {
  181: function updateTreasury(address newTreasury) external returns (bool) {    
  194: function updateWhitelistedFunction(bytes4 func, bool allowed) external returns (bool) {    
  206: function getEscrowed(address token) external view returns (uint256) {    
  216: function isWhitelisted(bytes4 func) external view returns(bool) {    

contracts\utils\CreditLib.sol
  125: function create(  
  168: function repay( 
  202: function withdraw(  

contracts\utils\SpigotedLineLib.sol
  169: function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {  
  194: function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) { 
  217: function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) { 
```