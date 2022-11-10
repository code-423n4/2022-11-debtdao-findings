## Use custom errors instead of string error messages

### Lines
- SpigotLib.sol:27
- SpigotLib.sol:25
- SpigotedLineLib.sol:13
- LineFactory.sol:13
- LineFactory.sol:14
- LineFactory.sol:12
- InterestRateCredit.sol:11
- InterestRateCredit.sol:7
- InterestRateCredit.sol:9
- EscrowLib.sol:25

## Use assembly to check for address(0)
### Lines
- SpigotLib.sol:180
- SpigotLib.sol:189
- SpigotLib.sol:201
- LineFactory.sol:27
- LineFactory.sol:141
- LineFactory.sol:146
- LineFactory.sol:183
- LineFactory.sol:33
- LineFactory.sol:30
- LineOfCredit.sol:445
- LineLib.sol:42
- LineLib.sol:67
- LineLib.sol:81

## Use `calldata` instead of `memory` for function arguments that do not get mutated.
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage. 
### Lines
- SpigotLib.sol:125
- Spigot.sol:125
- LineOfCredit.sol:218
- LineOfCredit.sol:483
- CreditLib.sol:203
- CreditLib.sol:240
- CreditLib.sol:74
- CreditLib.sol:169

## `unchecked{++i}` instead of `i++` (or use assembly when applicable)

Use `++i` instead of `i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.

### Lines
- CreditListLib.sol:23
- CreditListLib.sol:51
- LineOfCredit.sol:203
- LineOfCredit.sol:520
- LineOfCredit.sol:179
- EscrowLib.sol:57

## Right shift or Left shift instead of dividing or multiplying by powers of two
### Lines
- CreditLib.sol:117

## Use assembly to hash instead of Solidity
### Lines
- MutualConsent.sol:48
- MutualConsent.sol:45
- CreditLib.sol:69

