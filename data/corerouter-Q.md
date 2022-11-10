1. "Else" Keyword is not needed if there is "return" or "revert" before
https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotedLineLib.sol#L106
https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotedLineLib.sol#L110

There are "return" or "revert" statement before, so the "Else" keyword is not needed here. It is suggested to remove the keyword here for codes clarification.

2. Need to make sure token address is not zero address.

https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotedLineLib.sol#L217

It is suggested to add the following codes before the above line to make sure token address is not zero address.

if(token == address(0)) { revert TransferFailed(); }