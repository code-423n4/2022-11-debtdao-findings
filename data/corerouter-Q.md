1. "Else" Keyword is not needed if there is "return" or "revert" before
https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotedLineLib.sol#L106
https://github.com/code-423n4/2022-11-debtdao/blob/update-readme/contracts/utils/SpigotedLineLib.sol#L110

There are "return" or "revert" statement before, so the "Else" keyword is not needed here. It is suggested to remove the keyword here for codes clarification.

2. 