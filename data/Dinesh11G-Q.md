### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
Line-of-Credit/contracts/mock/SimpleRevenueContract.sol::17 => require(revenueToken.transfer(owner, revenueToken.balanceOf(address(this))), "Revenue: bad transfer");
Line-of-Credit/contracts/mock/SimpleRevenueContract.sol::19 => payable(owner).transfer(address(this).balance);
Line-of-Credit/contracts/mock/SimpleRevenueContract.sol::26 => require(revenueToken.transfer(owner, revenueToken.balanceOf(address(this))), "Revenue: bad transfer");
Line-of-Credit/contracts/mock/SimpleRevenueContract.sol::28 => payable(owner).transfer(address(this).balance);
Line-of-Credit/contracts/utils/LineLib.sol::48 => payable(receiver).transfer(amount);
Line-of-Credit/contracts/utils/SpigotedLineLib.sol::134 => IERC20(sellToken).approve(swapTarget, amount);
```
#### Tools used
Manual




### Unspecific Compiler Version Pragma

#### Impact
Issue Information: Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

Example
🤦 Bad:

pragma solidity ^0.8.0;
🚀 Good:

pragma solidity 0.8.4;

#### Findings:
```
Line-of-Credit/contracts/interfaces/IInterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ISpigot.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/interfaces/ISpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/mock/ZeroEx.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/LineOfCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SecuredLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/credit/SpigotedLine.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/interest-rate/InterestRateCredit.sol::1 => pragma solidity ^0.8.9;
Line-of-Credit/contracts/modules/oracle/Oracle.sol::2 => pragma solidity ^0.8.9;
::1 => pragma solidity ^0.8.9;
::1 => pragma solidity ^0.8.9;
2 => pragma solidity ^0.8.9;
::1 => pragma solidity ^0.8.9;
1 => pragma solidity ^0.8.9;
```
#### Tools used
Manual