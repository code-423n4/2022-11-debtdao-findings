## QA
### Missing checks for address(0x0) when assigning values to address state variables

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L55-L57
```solidity
File: /contracts/modules/credit/LineOfCredit.sol
55:        oracle = IOracle(oracle_);
56:        arbiter = arbiter_;
57:        borrower = borrower_;
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L36-L38
```solidity
File: /contracts/modules/spigot/Spigot.sol
36:        state.owner = _owner;
37:        state.operator = _operator;
38:        state.treasury = _treasury;
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L16
```solidity
File: /contracts/modules/oracle/Oracle.sol
16:        registry = FeedRegistryInterface(_registry);
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L49-L50
```solidity
File: /contracts/modules/escrow/Escrow.sol
49:        oracle = _oracle;
50:        borrower = _borrower;
```
### Constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. 

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L140
```solidity
File: /contracts/utils/CreditLib.sol

//@audit: 18
140:          decimals = 18;
//@audit: 18
145:          decimals = !passed ? 18 : abi.decode(result, (uint8));
```
### Open todos

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L140
```solidity
File: /contracts/modules/factories/LineFactory.sol
140:        // TODO: test

145:        // TODO: test
```
### Missing revert strings
Require statements should have descriptive reasons to aid in understanding why a particular action failed.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L112
```solidity
File: /contracts/modules/credit/LineOfCredit.sol
112:        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

241:        require(interestRate.setRate(id, drate, frate));

259:        require(interestRate.setRate(id, drate, frate));

326:        require(amount <= credit.principal + credit.interestAccrued);
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L62
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
62:        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

143:      require(amount <= unusedTokens[credit.token]);

160:        require(msg.sender == borrower);

239:        require(msg.sender == arbiter);
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L64
```solidity
File: /contracts/modules/credit/EscrowedLine.sol
64:    require(escrow_.liquidate(amount, targetToken, to));

90:    require(escrow.updateLine(newLine));
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L147
```solidity
File: /contracts/utils/SpigotedLineLib.sol
147:      require(ISpigot(spigot).updateOwner(newLine));
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L91
```solidity
File: /contracts/utils/EscrowLib.sol
91:        require(amount > 0);

105:        require(msg.sender == ILineOfCredit(self.line).arbiter());

161:        require(amount > 0);

198:        require(amount > 0);
```


### Natspec is incomplete/Missing
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L120-L127
```solidity
File: /contracts/utils/SpigotedLineLib.sol
//@audit: Missing @param amount, @param sellToken, @param swapTarget,@param zeroExTradeData
120:    function trade(
121:        uint256 amount,
122:        address sellToken,
123:        address payable swapTarget,
124:        bytes calldata zeroExTradeData
125:    ) 
126:        public
127:        returns(bool)

//@audit: Missing @param spigot, @param newLine
146:    function rollover(address spigot, address newLine) external returns(bool) {

//@audit: Missing @param spigot, @param arbiter
151:    function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) {

//@audit: Missing @param status, @param defaultSplit
169:    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

//@audit: Missing @param spigot, @param status, @param borrower, @param arbiter, @param to
194:    function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {

//@audit: Missing @param to, @param token, @param amount, @param status, @param borrower, @param arbiter
217:    function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L73-L81
```solidity
File: /contracts/utils/CreditLib.sol

//@audit: Missing @param credit, @param id, @param oracle, @param interestRate
//@audit: Missing @return c, @return principal,@return interest
73:  function getOutstandingDebt(
74:    ILineOfCredit.Credit memory credit,
75:    bytes32 id,
76:    address oracle,
77:    address interestRate
78:  )
79:    external
80:    returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)
81:  {


//@audit: Missing @param id, @param amount, @param lender, @param token
//@audit: Missing @return credit
125:  function create(
126:      bytes32 id,
127:      uint256 amount,
128:      address lender,
129:      address token,
130:      address oracle
131:  )
132:      external 
133:      returns(ILineOfCredit.Credit memory credit)
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L34-L38
```solidity
File:/contracts/modules/interest-rate/InterestRateCredit.sol

//@audit: Missing @param id, @param drawnBalance, @param facilityBalance
34:    function accrueInterest(
35:        bytes32 id,
36:        uint256 drawnBalance,
37:        uint256 facilityBalance
38:    ) external override onlyLineContract returns (uint256) {

//@audit: Missing @param id, @param dRate, @param fRate
74:    function setRate(
75:        bytes32 id,
76:        uint128 dRate,
77:        uint128 fRate
78:    ) external onlyLineContract returns (bool) {
```

### The nonReentrant modifier should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L93-L98
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
193:    function claimAndRepay(address claimToken, bytes calldata zeroExTradeData)
194:        external
195:        whileBorrowing
196:        nonReentrant
197:        returns (uint256)
198:    {
```

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L154-L159
```solidity
File: /contracts/modules/credit/SpigotedLine.sol
154:    function claimAndTrade(address claimToken, bytes calldata zeroExTradeData)
155:        external
156:        whileBorrowing
157:        nonReentrant
158:        returns (uint256)
159:    {
```
```

