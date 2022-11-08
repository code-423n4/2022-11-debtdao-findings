### G01: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706
#### prof
contracts/utils/EscrowLib.sol, 91, b'        require(amount > 0);'
contracts/utils/EscrowLib.sol, 119, b'                bool is4626 = tokenAddrBytes.length > 0 && passed;'
contracts/utils/EscrowLib.sol, 134, b'                if (decimalBytes.length > 0 && successDecimals) {'
contracts/utils/EscrowLib.sol, 161, b'        require(amount > 0);'
contracts/utils/EscrowLib.sol, 198, b'        require(amount > 0);'
contracts/modules/credit/LineOfCredit.sol, 132, b'        if (block.timestamp >= deadline && count > 0) {'
contracts/modules/credit/LineOfCredit.sol, 398, b'        if(facilityFee > 0) {'
contracts/modules/credit/LineOfCredit.sol, 484, b'        if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }'
contracts/modules/credit/LineOfCredit.sol, 487, b'        if (credit.deposit + credit.interestRepaid > 0) {'
contracts/modules/credit/LineOfCredit.sol, 526, b'                  credits[id].principal > 0 //`id` should be placed before `p` '

### G02: X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES
#### prof
contracts/modules/credit/SpigotedLine.sol, 125, b'            unusedTokens[credit.token] += newTokens - repaid;'
contracts/modules/credit/SpigotedLine.sol, 122, b'            unusedTokens[credit.token] -= repaid - newTokens;'
contracts/modules/credit/SpigotedLine.sol, 144, b'      unusedTokens[credit.token] -= amount;'
contracts/modules/credit/SpigotedLine.sol, 172, b'        unusedTokens[targetToken] += newTokens;'

### G03: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
contracts/utils/CreditListLib.sol, 23, b'      for(uint256 i; i < len; ++i) {'
contracts/utils/CreditListLib.sol, 51, b'        for(uint i = 1; i < len; ++i) {'
contracts/utils/EscrowLib.sol, 57, b'        for (uint256 i; i < length; ++i) {'
contracts/modules/credit/LineOfCredit.sol, 179, b'        for (uint256 i; i < len; ++i) {'
contracts/modules/credit/LineOfCredit.sol, 203, b'        for (uint256 i; i < len; ++i) {'
contracts/modules/credit/LineOfCredit.sol, 451, b'        unchecked { ++count; }'
contracts/modules/credit/LineOfCredit.sol, 499, b'        unchecked { --count; }'
contracts/modules/credit/LineOfCredit.sol, 520, b'        for (uint256 i; i <= lastSpot; ++i) {'

### G04：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
contracts/utils/CreditLib.sol, 69, b'    return keccak256(abi.encode(line, lender, token));'

### G05: FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
contracts/utils/LineFactoryLib.sol, 75, b'    function transferModulesToLine(address line, address spigot, address escrow) external '
contracts/utils/SpigotedLineLib.sol, 149, b'    function rollover(address spigot, address newLine) external returns(bool) '
contracts/utils/SpigotedLineLib.sol, 160, b'    function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) '
contracts/utils/SpigotedLineLib.sol, 183, b'    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) '
contracts/utils/SpigotedLineLib.sol, 208, b'    function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) '
contracts/utils/SpigotLib.sol, 80, b'    function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) '
contracts/utils/SpigotLib.sol, 102, b'    function claimRevenue(SpigotState storage self, address revenueContract, address token, bytes calldata data)\n        external\n        returns (uint256 claimed)\n    '
contracts/utils/SpigotLib.sol, 122, b'    function claimEscrow(SpigotState storage self, address token)\n        external\n        returns (uint256 claimed) \n    '
contracts/utils/SpigotLib.sol, 140, b'    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) '
contracts/utils/SpigotLib.sol, 161, b'    function removeSpigot(SpigotState storage self, address revenueContract)\n        external\n        returns (bool)\n    '
contracts/utils/SpigotLib.sol, 175, b'    function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)\n        external\n        returns(bool)\n    '
contracts/utils/SpigotLib.sol, 175, b'    function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)\n        external\n        returns(bool)\n    '
contracts/utils/SpigotLib.sol, 184, b'    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) '
contracts/utils/SpigotLib.sol, 184, b'    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) '
contracts/utils/SpigotLib.sol, 213, b'    function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) '
contracts/utils/SpigotLib.sol, 236, b'    function getSetting(SpigotState storage self, address revenueContract)\n        external view\n        returns(uint8, bytes4, bytes4)\n    '
contracts/modules/credit/SpigotedLine.sol, 76, b'    function _init() internal virtual override(LineOfCredit) returns(LineLib.STATUS) '
contracts/modules/credit/SpigotedLine.sol, 220, b'    function updateOwnerSplit(address revenueContract) external returns (bool) '
contracts/modules/credit/SpigotedLine.sol, 220, b'    function updateOwnerSplit(address revenueContract) external returns (bool) '
contracts/modules/factories/LineFactory.sol, 48, b'    function deployEscrow(\n        uint32 minCRatio,\n        address owner,\n        address borrower\n    ) external returns (address) '
contracts/modules/factories/LineFactory.sol, 48, b'    function deployEscrow(\n        uint32 minCRatio,\n        address owner,\n        address borrower\n    ) external returns (address) '
contracts/modules/factories/LineFactory.sol, 57, b'    function deploySpigot(\n        address owner,\n        address borrower,\n        address operator\n    ) external returns (address) '
contracts/modules/factories/LineFactory.sol, 57, b'    function deploySpigot(\n        address owner,\n        address borrower,\n        address operator\n    ) external returns (address) '
contracts/modules/spigot/Spigot.sol, 43, b'    function owner() external view returns (address) '
contracts/modules/spigot/Spigot.sol, 43, b'    function owner() external view returns (address) '
contracts/modules/spigot/Spigot.sol, 151, b'    function updateOwnerSplit(address revenueContract, uint8 ownerSplit)\n        external\n        returns(bool)\n    '
contracts/modules/spigot/Spigot.sol, 151, b'    function updateOwnerSplit(address revenueContract, uint8 ownerSplit)\n        external\n        returns(bool)\n    '
contracts/modules/spigot/Spigot.sol, 161, b'    function updateOwner(address newOwner) external returns (bool) '
contracts/modules/spigot/Spigot.sol, 161, b'    function updateOwner(address newOwner) external returns (bool) '

### G06: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem:
We can save getter function of public constants.
#### prof:
contracts/modules/credit/EscrowedLine.sol, 14, b'  IEscrow immutable public escrow;'
contracts/modules/credit/SpigotedLine.sol, 26, b'    ISpigot public immutable spigot;'
contracts/modules/credit/SpigotedLine.sol, 29, b'    address payable public immutable swapTarget;'
contracts/modules/credit/SpigotedLine.sol, 32, b'    uint8 public immutable defaultRevenueSplit;'
contracts/modules/credit/LineOfCredit.sol, 21, b'    uint256 public immutable deadline;'
contracts/modules/credit/LineOfCredit.sol, 23, b'    address public immutable borrower;'
contracts/modules/credit/LineOfCredit.sol, 25, b'    address public immutable arbiter;'
contracts/modules/credit/LineOfCredit.sol, 27, b'    IOracle public immutable oracle;'
contracts/modules/credit/LineOfCredit.sol, 29, b'    InterestRateCredit public immutable interestRate;'
contracts/modules/factories/LineFactory.sol, 16, b'    address public immutable arbiter;'
contracts/modules/factories/LineFactory.sol, 17, b'    address public immutable oracle;'
contracts/modules/factories/LineFactory.sol, 18, b'    address public immutable swapTarget;'
contracts/modules/escrow/Escrow.sol, 24, b'    uint32 public immutable minimumCollateralRatio;'
contracts/modules/escrow/Escrow.sol, 27, b'    address public immutable oracle;'
contracts/modules/escrow/Escrow.sol, 29, b'    address public immutable borrower;'
