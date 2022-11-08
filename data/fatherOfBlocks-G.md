**modules/LineOfCredit.sol**
- L112/241/259/326 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L132/398/484/487/526 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L487/491 - The operation credit.deposit + credit.interestRepaid is used multiple times, therefore a variable could be created in memory so as not to do the operation several times.



**modules/SpigotedLine.sol**
- L62/143/160/239 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**modules/EscrowedLine.sol**
- L64/90 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**modules/InterestRateCredit.sol**
- L26 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L28 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**utils/EscrowLib.sol**
- L91/119/134/161/198 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L91/105/161/198/216 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


**utils/SpigotLib.sol**
- L96/128/130/155/180/189/201 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.


