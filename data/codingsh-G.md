 ## G001: Don't Initialize Variables with Default Value
```solidity
  2022-11-debtdao/Line-of-Credit/contracts/tests/LineLib.t.sol::220 => for(uint256 i = 0; i < length; i++) {
  2022-11-debtdao/Line-of-Credit/contracts/tests/LineLib.t.sol::227 => for(uint256 i = 0; i < l; i++) {
  2022-11-debtdao/Line-of-Credit/contracts/tests/LineOfCredit.t.sol::97 => for(uint i = 0; i < amount; i++) {
  2022-11-debtdao/Line-of-Credit/contracts/tests/SecuredLine.t.sol::152 => uint count = 0;
  2022-11-debtdao/Line-of-Credit/contracts/tests/SecuredLine.t.sol::153 => for(uint i = 0; i < l;) {
  2022-11-debtdao/Line-of-Credit/contracts/tests/SecuredLine.t.sol::205 => for(uint i = 0; i < amount; i++) {
  2022-11-debtdao/Line-of-Credit/contracts/tests/SpigotedLine.t.sol::554 => uint256 interest = 0;
```
 ## G002: Cache Array Length Outside of Loop
```solidity
  2022-11-debtdao/Line-of-Credit/contracts/interfaces/ILineOfCredit.sol::234 => * @return - (uint, uint) - active credit lines, total length
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::31 => uint256 private count; // amount of open credit lines on a Line of Credit facility. ids.length includes null items
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::118 => return (count, ids.length);
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::171 => // use full length not count because positions might not be packed in order
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::172 => uint256 len = ids.length;
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::201 => uint256 len = ids.length;
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::517 => uint256 lastSpot = ids.length - 1;
  2022-11-debtdao/Line-of-Credit/contracts/modules/factories/LineFactory.sol::175 => @param ttl      - set total term length of line
  2022-11-debtdao/Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::60 => * @notice - total interest to accrue based on apr, balance, and length of time
  
  2022-11-debtdao/Line-of-Credit/contracts/utils/CreditListLib.sol::21 => uint256 len = ids.length;
  2022-11-debtdao/Line-of-Credit/contracts/utils/CreditListLib.sol::41 => uint256 len = ids.length ;
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::54 => uint256 length = self.collateralTokens.length;
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::57 => for (uint256 i; i < length; ++i) {
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::119 => bool is4626 = tokenAddrBytes.length > 0 && passed;
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::134 => if (decimalBytes.length > 0 && successDecimals) {
  2022-11-debtdao/Line-of-Credit/contracts/utils/LineFactoryLib.sol::38 => @param ttl      - set total term length of line
```


##  G006: Use immutable for OpenZeppelin AccessControl's Roles Declarations
```solidity
  2022-11-debtdao/Line-of-Credit/contracts/utils/CreditLib.sol::69 => return keccak256(abi.encode(line, lender, token));
```

## G007: Long Revert Strings
```solidity
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::5 => import { IEscrowedLine } from "../../interfaces/IEscrowedLine.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/EscrowedLine.sol::6 => import { ILineOfCredit } from "../../interfaces/ILineOfCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::11 => import {InterestRateCredit} from "../interest-rate/InterestRateCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::14 => import {ILineOfCredit} from "../../interfaces/ILineOfCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SecuredLine.sol::2 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SecuredLine.sol::8 => import { ILineOfCredit } from "../../interfaces/ILineOfCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SecuredLine.sol::9 => import { ISecuredLine } from "../../interfaces/ISecuredLine.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::4 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::11 => import {ISpigotedLine} from "../../interfaces/ISpigotedLine.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::12 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::13 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/escrow/Escrow.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/escrow/Escrow.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/escrow/Escrow.sol::8 => import {ILineOfCredit} from "../../interfaces/ILineOfCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/factories/LineFactory.sol::3 => import {ILineFactory} from "../../interfaces/ILineFactory.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/factories/LineFactory.sol::4 => import {IModuleFactory} from "../../interfaces/IModuleFactory.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/factories/ModuleFactory.sol::3 => import {IModuleFactory} from "../../interfaces/IModuleFactory.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::3 => import {IInterestRateCredit} from "../../interfaces/IInterestRateCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::28 => "InterestRateCred: only line contract."
  2022-11-debtdao/Line-of-Credit/contracts/modules/oracle/Oracle.sol::4 => import "chainlink/interfaces/FeedRegistryInterface.sol";
  2022-11-debtdao/Line-of-Credit/contracts/modules/spigot/Spigot.sol::3 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
   2022-11-debtdao/Line-of-Credit/contracts/utils/CreditLib.sol::5 => import { IInterestRateCredit } from "../interfaces/IInterestRateCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::4 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/EscrowLib.sol::5 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/LineFactoryLib.sol::3 => import {SecuredLine} from "../modules/credit/SecuredLine.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/LineLib.sol::2 => import { IInterestRateCredit } from "../interfaces/IInterestRateCredit.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/LineLib.sol::5 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/LineLib.sol::6 => import {SafeERC20}  from "openzeppelin/token/ERC20/utils/SafeERC20.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/SpigotLib.sol::3 => import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
  2022-11-debtdao/Line-of-Credit/contracts/utils/SpigotedLineLib.sol::6 => import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
```
 ## G008: Use Shift Right/Left instead of Division/Multiplication if possible
```solidity
  2022-11-debtdao/Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::100 => // Run whatever code is needed for the 2/2 consent
  2022-11-debtdao/Line-of-Credit/contracts/modules/oracle/Oracle.sol::24 => /* uint80 roundID */,
  2022-11-debtdao/Line-of-Credit/contracts/modules/oracle/Oracle.sol::26 => /* uint80 startedAt */,
  2022-11-debtdao/Line-of-Credit/contracts/modules/oracle/Oracle.sol::27 => /* uint80 timeStamp */,
  2022-11-debtdao/Line-of-Credit/contracts/utils/MutualConsent.sol::33 => // Run whatever code needed 2/2 consent
```


