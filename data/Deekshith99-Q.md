(Non-critical 1)
 In many solidity files, license keyword was not mentioned 

it should be mentioned as 
// SPDX-License-Identifier: UNLICENSED

(Non-critical 2)
Solidity pragma versioning should be upgraded to latest available version. Currently the solidity version in contracts is ^0.8.9  which was found to possess some bugs.

(Non-critical 3)
Solidity pragma versioning should be exactly same in all contracts. Currently some contracts use ^0.8.9 but some are fixed to 0.8.9.

(Non-critical 4)
Typos
(priviliegad is wrong)  it should be privileged 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L85
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L180