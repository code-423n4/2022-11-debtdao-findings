## [L-1] Unused/Empty RECEIVE()/FALLBACK() function.

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth))). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds.

***File : SpigotedLine.sol***

SpigotedLine.sol#L272




## [N-1] Use a more recent version of solidity.

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. Moreover, v0.8.10 has a series of bug.
eg. fix constructor source mappings for immutable, fix internal error when function names are not unique, improved error message for constant variables with mapping types ...



## [N-2] Use Scientific notation (e.g. 1E18) rather than Exponentiation (e.g. 10**18)

Scientific notation should be used for better code readability

***File : EscrowLib.sol***

EscrowLib.sol#L42




## [N-3] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

***File : InterestRateCredit.sol***

InterestRateCredit.sol#L9
InterestRateCredit.sol#L10



## [N-4] Use of block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

***File : LineOfCredit.sol***

LineOfCredit.sol#L58
LineOfCredit.sol#L132

***File : InterestRateCredit.sol***

InterestRateCredit.sol#L48
InterestRateCredit.sol#L50
InterestRateCredit.sol#L82
