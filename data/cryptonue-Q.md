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

# [NC] Open Todo
