### Cache Array Length Outside of Loop

#### Impact
Issue Information: Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::234 => * @return - (uint, uint) - active credit lines, total length
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::31 => uint256 private count; // amount of open credit lines on a Line of Credit facility. ids.length includes null items
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::118 => return (count, ids.length);
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::171 => // use full length not count because positions might not be packed in order
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::172 => uint256 len = ids.length;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::201 => uint256 len = ids.length;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::517 => uint256 lastSpot = ids.length - 1;
Line-of-Credit/contracts/modules/factories/LineFactory.sol::175 => @param ttl      - set total term length of line
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::60 => * @notice - total interest to accrue based on apr, balance, and length of time
Line-of-Credit/contracts/utils/CreditListLib.sol::21 => uint256 len = ids.length;
Line-of-Credit/contracts/utils/CreditListLib.sol::41 => uint256 len = ids.length ;
Line-of-Credit/contracts/utils/EscrowLib.sol::54 => uint256 length = self.collateralTokens.length;
Line-of-Credit/contracts/utils/EscrowLib.sol::57 => for (uint256 i; i < length; ++i) {
Line-of-Credit/contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
Line-of-Credit/contracts/utils/EscrowLib.sol::134 => if (decimalBytes.length > 0 && successDecimals) {
Line-of-Credit/contracts/utils/LineFactoryLib.sol::38 => @param ttl      - set total term length of line
```
#### Tools used
Manual



=================================================================================================






### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");
#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::132 => if (block.timestamp >= deadline && count > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::398 => if(facilityFee > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::484 => if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::487 => if (credit.deposit + credit.interestRepaid > 0) {
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::526 => credits[id].principal > 0 //`id` should be placed before `p`
::32 => vm.assume(dRate > 0 && dRate <= 1e4);
::727 => vm.assume(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::91 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
Line-of-Credit/contracts/utils/EscrowLib.sol::134 => if (decimalBytes.length > 0 && successDecimals) {
Line-of-Credit/contracts/utils/EscrowLib.sol::161 => require(amount > 0);
Line-of-Credit/contracts/utils/EscrowLib.sol::198 => require(amount > 0);
```
#### Tools used
Manual



=================================================================================================



### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: ⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
Line-of-Credit/contracts/utils/CreditLib.sol::69 => return keccak256(abi.encode(line, lender, token));
Line-of-Credit/contracts/utils/MutualConsent.sol::45 => bytes32 expectedHash = keccak256(abi.encodePacked(msg.data, nonCaller));
Line-of-Credit/contracts/utils/MutualConsent.sol::48 => bytes32 newHash = keccak256(abi.encodePacked(msg.data, msg.sender));
```
#### Tools used
Manual




=================================================================================================



### Long Revert Strings

#### Impact
Issue Information: Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}
#### Findings:
```
Line-of-Credit/contracts/mock/MockEscrowedLine.sol::3 => import  {EscrowedLine} from "../modules/credit/EscrowedLine.sol";
Line-of-Credit/contracts/mock/MockEscrowedLine.sol::5 => import {LineOfCredit} from "../modules/credit/LineOfCredit.sol";
Line-of-Credit/contracts/mock/RevenueToken.sol::3 => import "openzeppelin/token/ERC20/ERC20.sol";
Line-of-Credit/contracts/mock/SimpleRevenueContract.sol::3 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/mock/ZeroEx.sol::3 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::5 => import { IEscrowedLine } from "../../interfaces/IEscrowedLine.sol";
Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::6 => import { ILineOfCredit } from "../../interfaces/ILineOfCredit.sol";
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::11 => import {InterestRateCredit} from "../interest-rate/InterestRateCredit.sol";
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::14 => import {ILineOfCredit} from "../../interfaces/ILineOfCredit.sol";
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::2 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::8 => import { ILineOfCredit } from "../../interfaces/ILineOfCredit.sol";
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::9 => import { ISecuredLine } from "../../interfaces/ISecuredLine.sol";
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::4 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::11 => import {ISpigotedLine} from "../../interfaces/ISpigotedLine.sol";
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::12 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::13 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
Line-of-Credit/contracts/modules/escrow/Escrow.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/modules/escrow/Escrow.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
Line-of-Credit/contracts/modules/escrow/Escrow.sol::8 => import {ILineOfCredit} from "../../interfaces/ILineOfCredit.sol";
Line-of-Credit/contracts/modules/factories/LineFactory.sol::3 => import {ILineFactory} from "../../interfaces/ILineFactory.sol";
Line-of-Credit/contracts/modules/factories/LineFactory.sol::4 => import {IModuleFactory} from "../../interfaces/IModuleFactory.sol";
Line-of-Credit/contracts/modules/factories/ModuleFactory.sol::3 => import {IModuleFactory} from "../../interfaces/IModuleFactory.sol";
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::3 => import {IInterestRateCredit} from "../../interfaces/IInterestRateCredit.sol";
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::28 => "InterestRateCred: only line contract."
Line-of-Credit/contracts/modules/oracle/Oracle.sol::4 => import "chainlink/interfaces/FeedRegistryInterface.sol";
Line-of-Credit/contracts/modules/spigot/Spigot.sol::3 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
Line-of-Credit/contracts/utils/CreditLib.sol::5 => import { IInterestRateCredit } from "../interfaces/IInterestRateCredit.sol";
Line-of-Credit/contracts/utils/EscrowLib.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/utils/EscrowLib.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
Line-of-Credit/contracts/utils/LineFactoryLib.sol::3 => import {SecuredLine} from "../modules/credit/SecuredLine.sol";
Line-of-Credit/contracts/utils/LineLib.sol::2 => import { IInterestRateCredit } from "../interfaces/IInterestRateCredit.sol";
Line-of-Credit/contracts/utils/LineLib.sol::5 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
Line-of-Credit/contracts/utils/LineLib.sol::6 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
Line-of-Credit/contracts/utils/SpigotLib.sol::3 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
Line-of-Credit/contracts/utils/SpigotedLineLib.sol::6 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
```
#### Tools used
Manual



=================================================================================================



### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::100 => // Run whatever code is needed for the 2/2 consent
Line-of-Credit/contracts/modules/oracle/Oracle.sol::24 => /* uint80 roundID */,
Line-of-Credit/contracts/modules/oracle/Oracle.sol::26 => /* uint80 startedAt */,
Line-of-Credit/contracts/modules/oracle/Oracle.sol::27 => /* uint80 timeStamp */,
Line-of-Credit/contracts/utils/MutualConsent.sol::33 => // Run whatever code needed 2/2 consent
```
#### Tools used
Manual


