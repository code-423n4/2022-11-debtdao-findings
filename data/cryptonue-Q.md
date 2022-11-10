# [L] NO TRANSFER OWNERSHIP PATTERN

Recommend considering implementing a two step process where the owner or admin nominates an account and the nominated account needs to call an acceptOwnership() function for the transfer of ownership to fully succeed. This ensures the nominated EOA account is a valid and active account.

```solidity
File: SpigotLib.sol
178:     function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
179:         if(msg.sender != self.owner) { revert CallerAccessDenied(); }
180:         require(newOwner != address(0));
181:         self.owner = newOwner;
182:         emit UpdateOwner(newOwner);
183:         return true;
184:     }
```

# [L] UNSPECIFIC COMPILER VERSION PRAGMA

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```solidity
File: LineOfCredit.sol
1: pragma solidity ^0.8.9;

File: SecuredLine.sol
1: pragma solidity ^0.8.9;

File: SpigotedLine.sol
1: pragma solidity ^0.8.9;

File: InterestRateCredit.sol
1: pragma solidity ^0.8.9;

File: Oracle.sol
2: pragma solidity ^0.8.9;
```

# [L] UNUSED RECEIVE() FUNCTION

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```solidity
File: SpigotedLine.sol
272:     receive() external payable {}
```

```solidity
File: Spigot.sol
227:     receive() external payable {
228:         return;
229:     }
```

# [L] DECIMALS() NOT PART OF ERC20 STANDARD

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```solidity
File: CreditLib.sol
142:           (bool passed, bytes memory result) = token.call(
143:               abi.encodeWithSignature("decimals()")
144:           );
```

```solidity
File: EscrowLib.sol
131:                 (bool successDecimals, bytes memory decimalBytes) = deposit
132:                     .asset
133:                     .call(abi.encodeWithSignature("decimals()"));
```

# [NC] USE A MORE RECENT VERSION OF SOLIDITY

Most of the contract use 0.8.9 version of solidity, use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(,) Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

# [NC] TYPOS

Any typos in contract code (even for comments) might considered a low quality code/documentation.

- `availble`
- `doesnt`
- `prinicpal`
- `itselgf`
- `pat down debt`
- `dont`
- `fuly`

# [NC] UNUSED CODE COMMENT

It's better to remove commented code if it's not being used again

```solidity
File: EscrowedLine.sol
10: // import { SecuredLine } from "./SecuredLine.sol";
```

# [NC] LONG LINES

Source codes lines should be limited to a certain number of characters, usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. A good practice is to ensure the code does not require a horizontal scroll bar on GitHub. Even if it's for a comments it's better to follow this limit. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, longer than that should be split.

# [NC] INCONSISTENCY USING UINT AND UINT256

Most of the time it use `uint256` but some other part `uint`. Even though it's the same meaning, but a good consistency is better for a quality code. 

