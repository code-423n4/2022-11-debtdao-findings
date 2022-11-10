## [G-1] ++i/i++ should be UNCHECKED{++i}/UNCHECKED{i++} when it is not possible for them to overflow (eg. in for and while loops).

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves around 40 gas per loop.

***File : CreditListLib.sol***

CreditListLib.sol#L23
CreditListLib.sol#L51

***File : EscrowLib.sol***

EscrowLib.sol#L57

***File : LineOfCredit.sol***

LineOfCredit.sol#L179
LineOfCredit.sol#L203
LineOfCredit.sol#L520




## [G-2] Usage of UINT/INT smaller than 256bits incurs overhead.

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

***File : SpigotedLineLib.sol***

SpigotedLineLib.sol#L13

***File : SpigotedLine.sol***

SpigotedLine.sol#L60

***File : LineFactory.sol***

LineFactory.sol#L12
LineFactory.sol#L13
LineFactory.sol#L14

***File : SecuredLine.sol***

SecuredLine.sol#L21

***File : SpigotLib.sol***

SpigotLib.sol#L25




## [G-3] Non-strict inequalities are cheaper than strict ones.

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas). This also holds true between <= and <.

Various instances across the different contracts.



## [G-4] Duplicated require()/revert() checks should be refactored to a modifier to save gas.

When a require statement is used multiple times, it is cheaper in deployment costs to use a modifier instead.

***File : EscrowLib.sol***

require(amount>0);
EscrowLib.sol#L91
EscrowLib.sol#L161
EscrowLib.sol#L198

***File : LineOfCredit.sol***

require(interestrate.setrate(id,drate,frate));
LineOfCredit.sol#L241
LineOfCredit.sol#L259




## [G-5] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

***File : SpigotedLine.sol***

SpigotedLine.sol#L272



## [G-6] Use a more recent version of solidity.

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.