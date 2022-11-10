## Table of Contents
### Low Risk Issues
- Missing Zero Address Check 
- Admin Address Change should be a 2-Step Process
- Use fixed compiler versions instead of floating version
- Unused receive and fallback function

### Non-critical Issues
- Require Statements without Descriptive Revert Strings
- Define Magic Numbers to Constant
- Naming Convention for Constants
- Event is Missing Indexed Fields

&ensp;
## Low Risk Issues
### Missing Zero Address Check 

#### Issue
I recommend adding check of 0-address for input validation of critical address parameters.
Not doing so might lead to non-functional contract and have to redeploy the contract, when it is updated to 0-address accidentally.

#### PoC
Total of 13 instances found.
1. SpigotedLine.sol:constructor(): `spigot` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L64

2. SpigotedLine.sol:constructor(): `swapTarget` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L66

3. LineOfCredit.sol:constructor(): `borrower` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L57

4. LineOfCredit.sol:constructor(): `arbiter` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L56

5. LineOfCredit.sol:constructor(): `oracle` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L55

6. EscrowedLine.sol:constructor(): `escrow` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L17

7. Spigot.sol:constructor(): `state.owner` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L36

8. Spigot.sol:constructor(): `state.operator` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L37

9. Spigot.sol:constructor(): `state.treasury` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L38

10. Escrow.sol:constructor(): `oracle` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L49

11. Escrow.sol:constructor(): `borrower` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L50

12. Escrow.sol:constructor(): `state.line` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L51

13. LineFactory.sol:constructor(): `factory` address 
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L26

#### Mitigation
Add 0-address check for above addresses.

&ensp;
### Admin Address Change should be a 2-Step Process

#### Issue
High privilege account such as admin / owner is changed with only single process.
This can be a concern since an admin / owner role has a high privilege in the contract and
mistakenly setting a new admin to an incorrect address will end up losing that privilege.

#### PoC
1. `owner` privilege of Spigot.sol
```solidity
    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }
        require(newOwner != address(0));
        self.owner = newOwner;
        emit UpdateOwner(newOwner);
        return true;
    }
```
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L159-L161
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L178-L184

#### Mitigation
This can be fixed by implementing 2-step process. We can do this by following. 
First make the function approve a new address as a pending admin/owner.
Next that pending admin/owner has to claim the ownership in a separate transaction to be a new admin/owner.

&ensp;
### Use fixed compiler versions instead of floating version

#### Issue
It is best practice to lock your pragma instead of using floating pragma.
The use of floating pragma has a risk of accidentally get deployed using latest complier
which may have higher risk of undiscovered bugs.
Reference: https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

#### PoC
Total of 5 instances found.
```
./Oracle.sol:2:pragma solidity ^0.8.9;
./SpigotedLine.sol:1:pragma solidity ^0.8.9;
./SecuredLine.sol:1:pragma solidity ^0.8.9;
./LineOfCredit.sol:1:pragma solidity ^0.8.9;
./InterestRateCredit.sol:1:pragma solidity ^0.8.9;
```

#### Mitigation
I suggest to lock your pragma and aviod using floating pragma.
```
// bad
pragma solidity ^0.8.10;

// good
pragma solidity 0.8.10;
```

&ensp;
### Unused receive and fallback function

#### Issue
If the intention is for the Ether to be used, the function should call another function, 
otherwise it should revert to prevent user's ETH getting locked up in the contract.

#### PoC
Total of 2 instances found.
```
./Spigot.sol:227:    receive() external payable {
./SpigotedLine.sol:272:    receive() external payable {}
```

&ensp;
## Non-critical Issues
### Require Statements without Descriptive Revert Strings

#### Issue
It is best practice to include descriptive revert strings for require statement for readability and auditing.

#### PoC
Total of 23 instances found.
```solidity
./SpigotedLineLib.sol:147:      require(ISpigot(spigot).updateOwner(newLine));
./EscrowedLine.sol:64:    require(escrow_.liquidate(amount, targetToken, to));
./EscrowedLine.sol:90:    require(escrow.updateLine(newLine));
./SpigotedLine.sol:62:        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);
./SpigotedLine.sol:143:      require(amount <= unusedTokens[credit.token]);
./SpigotedLine.sol:160:        require(msg.sender == borrower);
./SpigotedLine.sol:239:        require(msg.sender == arbiter);
./EscrowLib.sol:91:        require(amount > 0);
./EscrowLib.sol:105:        require(msg.sender == ILineOfCredit(self.line).arbiter());
./EscrowLib.sol:161:        require(amount > 0);
./EscrowLib.sol:198:        require(amount > 0);
./EscrowLib.sol:216:      require(msg.sender == self.line);
./SpigotLib.sol:96:            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
./SpigotLib.sol:128:        require(revenueContract != address(this));
./SpigotLib.sol:130:        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
./SpigotLib.sol:155:        require(success);
./SpigotLib.sol:180:        require(newOwner != address(0));
./SpigotLib.sol:189:        require(newOperator != address(0));
./SpigotLib.sol:201:        require(newTreasury != address(0));
./LineOfCredit.sol:112:        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));
./LineOfCredit.sol:241:        require(interestRate.setRate(id, drate, frate));
./LineOfCredit.sol:259:        require(interestRate.setRate(id, drate, frate));
./LineOfCredit.sol:326:        require(amount <= credit.principal + credit.interestAccrued);
```

#### Mitigation
Add descriptive revert strings to easier understand what the code is trying to do.

&ensp;
### Define Magic Numbers to Constant

#### Issue
It is best practice to define magic numbers to constant rather than just using as a magic number.
This improves code readability and maintainability. 

#### PoC
1. Magic Number: 100
```solidity
./SpigotLib.sol:90:        uint256 escrowedAmount = claimed * self.settings[revenueContract].ownerSplit / 100;
```

#### Mitigation
Define magic numbers to constant.

&ensp;
### Naming Convention for Constants

#### Issue
Constants should be named with all capital letters with underscores separating words.

Solidity documentation:
https://docs.soliditylang.org/en/v0.8.15/style-guide.html#constants

#### PoC
Below 8 constant variable should follow constants naming convention best practice.
```solidity
./LineFactory.sol:12:    uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment
./LineFactory.sol:14:    uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio
```

&ensp;
### Event is Missing Indexed Fields

#### Issue
Each event should have 3 indexed fields if there are 3 or more fields.

#### PoC
Total of 4 instances found.
```solidity
./MutualConsent.sol:21:    event MutualConsentRegistered(
                               bytes32 _consentHash
                           );
./SpigotLib.sol:241:    event AddSpigot(
                            address indexed revenueContract,
                            uint256 ownerSplit
                        );
./SpigotLib.sol:255:    event ClaimRevenue(
                            address indexed token,
                            uint256 indexed amount,
                            uint256 escrowed,
                            address revenueContract
                        );
./SpigotLib.sol:262:    event ClaimEscrow(
                            address indexed token,
                            uint256 indexed amount,
                            address owner
                        );
```

#### Mitigation
Add up to 3 indexed fields when possible.

&ensp;