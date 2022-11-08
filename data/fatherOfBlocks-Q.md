**modules/LineOfCredit.sol**
- L3 - The Denominations library is imported but it is never used, therefore it should be deleted.

- L49-62 - In the constructor several variables are set in storage (oracle, timestamp, borrower and arbriter) that are immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS ).

- L99/101 - The modifier has an if that inside has the execution of the code, the structure is incorrect, since the if should be negated and inside revert and if it does not revert outside the if, execute the code.

- L112/241/259/326 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**modules/SpigotedLine.sol**
- L3/7 - The Denominations and CreditLib libraries are imported but they are never used, therefore they should be deleted.

- L53-67 - In the constructor several variables are set in storage (spigot, defaultRevenueSplit and swapTarget) that are immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).

- L62/143/160/239 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**modules/EscrowedLine.sol**
- L1 - All contracts (SpigotedLine.sol, LineOfCredit.sol and SecuredLine.sol) use the pragma version solidity ^0.8.9; but this contract has version pragma solidity 0.8.9; this could generate certain problems in the deploy.

- L10 - An import was made that is commented out, this can cause confusion, therefore it is best to delete it.

- L17 - In the constructor, the variable is set in storage escrow, which is immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).

- L64/90 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**modules/LineFactory.sol**
- L3/5 - The ILineFactory and LineLib libraries are imported but they are never used, therefore they should be deleted.

- L26 - In the constructor, the variable in storage factory is set, which is immutable, therefore it should be validated that the entered address is not zero, since it would make multiple functions unusable (DoS).


**modules/Escrow.sol**
- L3/7/8/9/10 - Dominations, IOracle, ILineOfCredit, CreditLib and LineLib libraries are imported but they are never used, therefore they should be deleted.

- L42 - In the constructor the variables are set in storage: minimumCollateralRatio, oracle and borrower that are immutable, therefore it should be validated that the addresses that are entered are not zero, since it would make multiple functions unusable (DoS).


**modules/Spigot.sol**
- L4 - The LineLib library is imported but it is never used, therefore it should be deleted.


**utils/LineLib.sol**
- L1 - All contracts (SpigotedLine.sol, LineOfCredit.sol and SecuredLine.sol) use the pragma version solidity ^0.8.9; but this contract has version pragma solidity 0.8.9; this could generate certain problems in the deploy.

- L2/3/4 - The interfaces IInterestRateCredit, IOracle and ILineOfCredit are imported but they are never used, therefore they should be deleted.


**utils/CreditLib.sol**
- L7 - The LineLib library is imported but it is never used, therefore it should be deleted.

- L3/6 - The ILineOfCredit interface is imported twice, therefore it should be deleted once.

- L117 - A division is performed depending on an input, decimals, therefore it should be validated that decimals != 0 and send a message if that happens.


**utils/CreditListLib.sol**
- L2/3/4 - The CreditLib, IOracle and ILineOfCredit interfaces are imported but are never used, so they should be deleted.
	

**utils/EscrowLib.sol**
- L91/105/161/198/216 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.


**utils/SpigotLib.sol**
- L96/128/130/155/180/189/201 - When we use a require and throw an exception it is important to show a understandable message, this is important because it makes the user better understand the reason why it is reverted.

- L285 - Error UnclaimedRevenue(); but it is never used in all contracts, so it should be removed.


**utils/SpigotedLineLib.sol**
- L4 - The IspigotedLine interface is imported but it is never used, therefore it should be deleted.
