Issue1:
Use more recent versions of solidity:

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives security fixes. Furthermore, breaking changes as well as new features are introduced regularly (Consider using 0.8.16 or 0.8.17).

Issue2:
Typos:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/interfaces/ISecuredLine.sol#L16

Line is not completed (if function executed successfully).

Issue3:
Function doesn't check if revenue contract is a valid address:

https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L125
https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L143
https://github.com/debtdao/Line-of-Credit/blob/master/contracts/utils/SpigotLib.sol#L164
