# [rotcivegaf] QA report

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | Open TODO | 2 |
| [L&#x2011;02] | Send ether with `call` instead of `transfer` | 1 |

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Lint | n |
| [N&#x2011;02] | Unused imports | 23 |
| [N&#x2011;03] | Non-library/interface files should use fixed compiler versions, not floating ones | 4 |
| [N&#x2011;04] | Unreachable code | 2 |
| [N&#x2011;05] | Don't check `address(0)` | 1 |
| [N&#x2011;06] | Use `internal` modifier in the library function instead of `external`/`public` | 46 |
| [N&#x2011;07] | Require without message/custom errors | 23 |

## Low Risk

### [L-01] Open TODO

Open TODO can point to architecture or programming issues that still need to be resolved.

```solidity
File: contracts/modules/factories/LineFactory.sol

140        // TODO: test

145        // TODO: test
```

### [L-02] Send ether with `call` instead of `transfer`

Use call instead of transfer to send ether. And return value must be checked if sending ether is successful or not.
Sending ether with the transfer is no longer recommended.

This transaction will fail inevitably when:
1) The `_to` smart contract does not implement a payable function.
2) `_to` smart contract does implement a payable fallback which uses more than 2300 gas unit.
3) The `_to` smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call’s gas usage above 2300.

```solidity
File: contracts/utils/LineLib.sol

48            payable(receiver).transfer(amount);
```

## Non-critical

### [N‑01] Lint

Wrong identation: All contracts in the scope haves use both 2 and 4 spaces for indentation, use always 4 spaces

Remove space:

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

10     // = 31557600 * 10000 = 315576000000;
```

```solidity
File: contracts/modules/oracle/Oracle.sol

33 \n

34 \n
```

```solidity
File: contracts/utils/CreditLib.sol

 15 \n

 30 \n

 35 \n

 53 \n

114    public  pure
```

```solidity
File: contracts/utils/CreditListLib.sol

41      uint256 len = ids.length ;
```

```solidity
File: contracts/utils/SpigotedLineLib.sol

222 \n
```

### [N‑02] Unused imports

```solidity
File: contracts/utils/CreditLib.sol

7 import { LineLib } from "./LineLib.sol";
```

```solidity
File: contracts/modules/factories/LineFactory.sol

5 import {LineLib} from "../../utils/LineLib.sol";

7 import {SecuredLine} from "../credit/SecuredLine.sol";
```

```solidity
File: contracts/modules/escrow/Escrow.sol

 3 import { Denominations } from "chainlink/Denominations.sol";

 7 import {IOracle} from "../../interfaces/IOracle.sol";

 8 import {ILineOfCredit} from "../../interfaces/ILineOfCredit.sol";

 9 import {CreditLib} from "../../utils/CreditLib.sol";

10 import {LineLib} from "../../utils/LineLib.sol";
```

```solidity
File: contracts/modules/spigot/Spigot.sol

4 import { LineLib } from  "../../utils/LineLib.sol";
```

```solidity
File: contracts/modules/credit/SpigotedLine.sol

3 import { Denominations } from "chainlink/Denominations.sol";

7 import {CreditLib} from "../../utils/CreditLib.sol";

9 import {MutualConsent} from "../../utils/MutualConsent.sol";
```

```solidity
File: contracts/modules/credit/SecuredLine.sol

2 import { IERC20 } from "openzeppelin/token/ERC20/IERC20.sol";
```

```solidity
File: contracts/modules/credit/LineOfCredit.sol

3 import { Denominations } from "chainlink/Denominations.sol";
```

```solidity
File: contracts/modules/credit/EscrowedLine.sol

9 import { LineOfCredit } from "./LineOfCredit.sol";
```

```solidity
File: contracts/utils/SpigotLib.sol

3 import { ReentrancyGuard } from "openzeppelin/security/ReentrancyGuard.sol";
```

```solidity
File: contracts/utils/SpigotedLineLib.sol

4 import { ISpigotedLine } from "../interfaces/ISpigotedLine.sol";
```

```solidity
File: contracts/utils/LineLib.sol

2 import { IInterestRateCredit } from "../interfaces/IInterestRateCredit.sol";

3 import { ILineOfCredit } from "../interfaces/ILineOfCredit.sol";

4 import { IOracle } from "../interfaces/IOracle.sol";
```

```solidity
File: contracts/utils/CreditListLib.sol

2 import { ILineOfCredit } from "../interfaces/ILineOfCredit.sol";

3 import { IOracle } from "../interfaces/IOracle.sol";

4 import { CreditLib } from "./CreditLib.sol";
```

### [N‑03] Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
File: contracts/modules/oracle/Oracle.sol

2 pragma solidity ^0.8.9;
```

```solidity
File: contracts/modules/interest-rate/InterestRateCredit.sol

1 pragma solidity ^0.8.9;
```

```solidity
File: contracts/modules/credit/SpigotedLine.sol

1 pragma solidity ^0.8.9;
```

```solidity
File: contracts/modules/credit/LineOfCredit.sol

1 pragma solidity ^0.8.9;
```

### [N‑04] Unreachable code

```solidity
File: contracts/utils/SpigotedLineLib.sol

207        return false;

234        return false;
```

### [N‑05] Don't check `address(0)`

```solidity
File: contracts/modules/factories/LineFactory.sol

21        address moduleFactory,
```

### [N‑06] Use `internal` modifier in the library function instead of `external`/`public`

```solidity
File: contracts/utils/CreditListLib.sol

20    function removePosition(bytes32[] storage ids, bytes32 id) external returns(bool) {

40    function stepQ(bytes32[] storage ids) external returns(bool) {
```

```solidity
File: contracts/utils/CreditLib.sol

66    external pure

79    external

114    public  pure

132      external

173    external

207    external

244    public
```

```solidity
File: contracts/utils/EscrowLib.sol

 34    function _getLatestCollateralRatio(EscrowState storage self, address oracle) public returns (uint256) {

 51    function _getCollateralValue(EscrowState storage self, address oracle) public returns (uint256) {

 88        external

104    function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {

160    ) external returns (uint256) {

182    function getCollateralRatio(EscrowState storage self, address oracle) external returns (uint256) {

187    function getCollateralValue(EscrowState storage self, address oracle) external returns (uint256) {

197    ) external returns (bool) {

210    function isLiquidatable(EscrowState storage self, address oracle, uint256 minimumCollateralRatio) external returns(bool) {

215    function updateLine(EscrowState storage self, address _line) external returns(bool) {
```

```solidity
File: contracts/utils/LineLib.sol

39      external

64      external

80    function getBalance(address token) external view returns (uint256) {
```

```solidity
File: contracts/utils/SpigotedLineLib.sol

 61        external

126        public

146    function rollover(address spigot, address newLine) external returns(bool) {

151    function canDeclareInsolvent(address spigot, address arbiter) external view returns (bool) {

169    function updateSplit(address spigot, address revenueContract, LineLib.STATUS status, uint8 defaultSplit) external returns (bool) {

194    function releaseSpigot(address spigot, LineLib.STATUS status, address borrower, address arbiter, address to) external returns (bool) {

217    function sweep(address to, address token, uint256 amount, LineLib.STATUS status, address borrower, address arbiter) external returns (bool) {
```

```solidity
File: contracts/utils/SpigotLib.sol

 30        public

 61    function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) {

 84        external

106        external

125    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {

144        external

165        external

178    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {

187    function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {

196    function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {

208    function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {

216    function getEscrowed(SpigotState storage self, address token) external view returns (uint256) {

221    function isWhitelisted(SpigotState storage self, bytes4 func) external view returns(bool) {

228        external view
```

```solidity
File: contracts/utils/LineFactoryLib.sol

47    ) external returns(address) {

57    function transferModulesToLine(address line, address spigot, address escrow) external {

86    ) external returns(address){
```

### [N‑07] Require without message/custom errors

Add message/use custom errors to give the idea what was the cause of failure

```solidity
File: contracts/modules/credit/EscrowedLine.sol

64    require(escrow_.liquidate(amount, targetToken, to));

90    require(escrow.updateLine(newLine));
```

```solidity
File: contracts/modules/credit/LineOfCredit.sol

112        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

241        require(interestRate.setRate(id, drate, frate));

259        require(interestRate.setRate(id, drate, frate));

326        require(amount <= credit.principal + credit.interestAccrued);
```

```solidity
File: contracts/modules/credit/SpigotedLine.sol

 62        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

143      require(amount <= unusedTokens[credit.token]);

160        require(msg.sender == borrower);

239        require(msg.sender == arbiter);
```

```solidity
File: contracts/utils/EscrowLib.sol

 91        require(amount > 0);

105        require(msg.sender == ILineOfCredit(self.line).arbiter());

161        require(amount > 0);

198        require(amount > 0);

216      require(msg.sender == self.line);
```

```solidity
File: contracts/utils/SpigotedLineLib.sol

147      require(ISpigot(spigot).updateOwner(newLine));
```

```solidity
File: contracts/utils/SpigotLib.sol

 96            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));

128        require(revenueContract != address(this));

130        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));

155        require(success);

180        require(newOwner != address(0));

189        require(newOperator != address(0));

201        require(newTreasury != address(0));
```

