## USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

Including all files in the scope.

## REPLACE `MODIFIER` WITH `FUNCTION`
Modifiers make code more elegant, but cost more than normal functions.

Instances include:
[`contracts/modules/credit/LineOfCredit.sol:78`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L78)
[`contracts/modules/credit/LineOfCredit.sol:83`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L83)
[`contracts/modules/credit/LineOfCredit.sol:88`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L88)
[`contracts/modules/credit/LineOfCredit.sol:98`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L98)
[`contracts/modules/interest-rate/InterestRateCredit.sol:25`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L25)
[`contracts/utils/MutualConsent.sol:31`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L31)  

## Always put your `require` statement on top in the function
Instances include:
[`contracts/modules/credit/LineOfCredit.sol:259`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L259)

## Use assembly to check for address(0)  
Saves 6 gas per instance if using assembly to check for address(0)  
e.g.  
  
```  
assembly {  
 if iszero(_addr) {  
  mstore(0x00, "zero address")  
  revert(0x00, 0x20)  
 }  
}  
```  
  
Instances include:
[`contracts/modules/credit/LineOfCredit.sol:445`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L445)
[`contracts/modules/factories/LineFactory.sol:27`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L27)
[`contracts/modules/factories/LineFactory.sol:30`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L30)
[`contracts/modules/factories/LineFactory.sol:33`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L33)
[`contracts/modules/factories/LineFactory.sol:141`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L141)
[`contracts/modules/factories/LineFactory.sol:146`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L146)
[`contracts/utils/LineLib.sol:42`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L42)
[`contracts/utils/LineLib.sol:67`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L67)
[`contracts/utils/LineLib.sol:81`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L81)
[`contracts/utils/SpigotLib.sol:189`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189)
[`contracts/utils/SpigotLib.sol:201`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201)

## Use selfbalance()
Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.
Instances include:
[`contracts/utils/LineLib.sol:84`](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L84)
