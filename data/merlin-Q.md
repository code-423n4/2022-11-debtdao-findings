# Issue 1
## Title 
Unreachable code
## Links to affected code
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L207
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L234

## Impact
The SpigotedLineLib library has releaseSpigot() and sweep() functions that has a part of the code which can never be executed. (return false;)

## Tools Used
Slither, the Solidity source analyzer

## Recommended Mitigation Steps
Consider removing the useless code.


# Issue 2
## Title 
SPDX license identifier not provided smart contracts

## Impact
All contracts do not have SPDX license identifier.

## Tools Used
Slither, the Solidity source analyzer

## Recommended Mitigation Steps
Consider adding SPDX license identifier to all smart contracts.