## Table of Contents
- Duplicate require() Checks Should be a Modifier or a Function
- X = X + Y costs less gass than X += Y
- Internal Function Called Only Once can be Inlined
- Use Calldata instead of Memory for Read Only Function Parameters
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Empty Blocks Should Emit Something or be Removed

&ensp;
### Duplicate require() Checks Should be a Modifier or a Function

#### Issue
Since below require checks are used more than once,
I recommend making these to a modifier or a function.

#### PoC
Total of 2 instances found.
```solidity
./EscrowLib.sol:91:        require(amount > 0);
./EscrowLib.sol:161:        require(amount > 0);
./EscrowLib.sol:198:        require(amount > 0);
```
```solidity
./LineOfCredit.sol:241:        require(interestRate.setRate(id, drate, frate));
./LineOfCredit.sol:259:        require(interestRate.setRate(id, drate, frate));
```

#### Mitigation
I recommend making duplicate require statement into modifier or a function.

&ensp;
### X = X + Y costs less gass than X += Y

#### Issue
X = X + Y costs less gass than X += Y for state variables

#### PoC
1. SpigotedLine.sol:claimAndRepay()
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L125
```solidity
./SpigotedLine.sol:122:            unusedTokens[credit.token] -= repaid - newTokens;
./SpigotedLine.sol:125:            unusedTokens[credit.token] += newTokens - repaid;
```
Change to
```solidity
unusedTokens[credit.token] = unusedTokens[credit.token] - repaid - newTokens;
unusedTokens[credit.token] = unusedTokens[credit.token] + newTokens - repaid;
```
Before avg: 90215
After  avg: 86139
Save 4076 gas

2. SpigotedLine.sol:claimAndTrade()
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L172
```solidity
./SpigotedLine.sol:172:        unusedTokens[targetToken] += newTokens;
```
Change to
```solidity
unusedTokens[targetToken] = unusedTokens[targetToken] + newTokens;
```
Before avg: 110350
After  avg: 109902
Save 448 gas

3. LineOfCredit.sol:increaseCredit()
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L276
```solidity
./LineOfCredit.sol:276:        credit.deposit += amount;
```
Change to
```solidity
credit.deposit = credit.deposit + amount;
```
Before avg: 17555
After  avg: 17553
Save 2 gas

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 4 instances found.

1. _rollover() of EscrowedLine.sol
This function called only once at line 59 of SecuredLine.sol.
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L89
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L59

2. _getNonCaller() of MutualConsent.sol
This function called only once at line 41
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L65
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/MutualConsent.sol#L41

3. _sortIntoQ() of LineOfCredit.sol
This function called only once at line 364
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L516
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L364

4. _accrueInterest() of InterestRateCredit.sol
This function called only once at line 39
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L42
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L39

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Calldata instead of Memory for Read Only Function Parameters

#### Issue
It is cheaper gas to use calldata than memory if the function parameter is read only.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, 
and behaves mostly like memory. More details on following link.
link: https://docs.soliditylang.org/en/v0.8.15/types.html#data-location

#### PoC
Total of 5 instances found.
```
./Spigot.sol:125:    function addSpigot(address revenueContract, Setting memory setting) external returns (bool) {
./SpigotLib.sol:125:    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
./CreditLib.sol:74:    ILineOfCredit.Credit memory credit,
./CreditLib.sol:169:    ILineOfCredit.Credit memory credit,
./CreditLib.sol:203:    ILineOfCredit.Credit memory credit,
```

#### Mitigation
Change memory to calldata

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size. Therefore it is more gas saving to use 32 bytes 
unless it is used in a struct or state variable in order to reduce storage slot. 

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 29 instances found.
```
./LineFactoryLib.sol:51:        uint8 split = SecuredLine(oldLine).defaultRevenueSplit();
./LineFactoryLib.sol:85:        uint8 revenueSplit
./SpigotedLineLib.sol:13:    uint8 constant MAX_SPLIT = 100;
./SpigotedLineLib.sol:169:    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {
./SpigotedLineLib.sol:170:        (uint8 split,, bytes4 transferFunc) = ISpigot(spigot).getSetting(revenueContract);
./Spigot.sol:146:    function updateOwnerSplit(address revenueContract, uint8 ownerSplit)
./Spigot.sol:222:        returns(uint8, bytes4, bytes4)
./Escrow.sol:24:    uint32 public immutable minimumCollateralRatio;
./Escrow.sol:43:        uint32 _minimumCollateralRatio,
./SpigotedLine.sol:32:    uint8 public immutable defaultRevenueSplit;
./SpigotedLine.sol:60:        uint8 defaultRevenueSplit_
./LineFactory.sol:12:    uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment
./LineFactory.sol:13:    uint8 constant MAX_SPLIT = 100; // max % to take
./LineFactory.sol:14:    uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio
./LineFactory.sol:43:        uint32 minCRatio,
./LineFactory.sol:71:        uint8 split = defaultRevenueSplit; // gas savings
./SecuredLine.sol:21:        uint8 defaultSplit_
./SpigotLib.sol:25:    uint8 constant MAX_SPLIT = 100;
./SpigotLib.sol:164:    function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
./SpigotLib.sol:229:        returns(uint8, bytes4, bytes4)
./SpigotLib.sol:252:        uint8 indexed split
./CreditLib.sol:112:    uint8 decimals
./CreditLib.sol:138:      uint8 decimals;
./LineOfCredit.sol:224:        uint128 drate,
./LineOfCredit.sol:225:        uint128 frate,
./LineOfCredit.sol:249:        uint128 drate,
./LineOfCredit.sol:250:        uint128 frate
./InterestRateCredit.sol:76:        uint128 dRate,
./InterestRateCredit.sol:77:        uint128 fRate
```

#### Mitigation
I suggest using uint256 instead of anything smaller and downcast where needed.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 1 instance found.
```solidity
./SpigotedLine.sol:272:    receive() external payable {}
```

#### Mitigation
Add emit or revert in the function block.

&ensp;