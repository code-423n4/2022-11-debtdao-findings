### N01: NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES
contracts/modules/credit/SpigotedLine.sol, 1, b'ragma solidity ^0.8.9;'
contracts/modules/credit/SecuredLine.sol, 1, b'ragma solidity ^0.8.9;'
contracts/modules/credit/LineOfCredit.sol, 1, b'ragma solidity ^0.8.9;'
contracts/modules/interest-rate/InterestRateCredit.sol, 1, b'ragma solidity ^0.8.9;'
contracts/modules/oracle/Oracle.sol, 2, b'pragma solidity ^0.8.9;'
contracts/interfaces/ISpigot.sol, 1, b'ragma solidity ^0.8.9;'
contracts/interfaces/IInterestRateCredit.sol, 1, b'ragma solidity ^0.8.9;'
contracts/interfaces/ISpigotedLine.sol, 1, b'ragma solidity ^0.8.9;'

## low risk foundings
### L01: UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
#### problem
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
#### prof
contracts/modules/credit/SpigotedLine.sol, 272, b'    receive() external payable {}'
contracts/modules/spigot/Spigot.sol, 229, b'    receive() external payable {\n        return;\n    }'

### L02: address variable should check if it is zero MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES
#### prof
contracts/modules/credit/EscrowedLine.sol, 17, b'    escrow = IEscrow(_escrow);'
contracts/modules/credit/SpigotedLine.sol, 64, b'        spigot = ISpigot(spigot_);'
contracts/modules/credit/SpigotedLine.sol, 66, b'        swapTarget = swapTarget_;'
contracts/modules/credit/LineOfCredit.sol, 55, b'        oracle = IOracle(oracle_);'
contracts/modules/credit/LineOfCredit.sol, 56, b'        arbiter = arbiter_;'
contracts/modules/credit/LineOfCredit.sol, 57, b'        borrower = borrower_;'
contracts/modules/credit/LineOfCredit.sol, 59, b'        interestRate = new InterestRateCredit();'
contracts/modules/interest-rate/InterestRateCredit.sol, 20, b'        lineContract = msg.sender;'
contracts/modules/oracle/Oracle.sol, 16, b'        registry = FeedRegistryInterface(_registry);'
contracts/modules/factories/LineFactory.sol, 26, b'        factory = IModuleFactory(moduleFactory);'
contracts/modules/factories/LineFactory.sol, 36, b'        arbiter = arbiter_;'
contracts/modules/factories/LineFactory.sol, 37, b'        oracle = oracle_;'
contracts/modules/factories/LineFactory.sol, 38, b'        swapTarget = swapTarget_;'
contracts/modules/escrow/Escrow.sol, 49, b'        oracle = _oracle;'
contracts/modules/escrow/Escrow.sol, 50, b'        borrower = _borrower;'