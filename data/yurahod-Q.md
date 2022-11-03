# Missing events

## Issue Details
Events are inheritable members of contracts. When you call them, they cause the arguments to be stored in the transaction’s log — a special data structure in the blockchain.
These logs are associated with the address of the contract which can then be used by developers and auditors to keep track of the transactions.
The contract `LineFactoryLib` was found to be missing these events on the function `transferModulesToLine` which would make it difficult or impossible to track these transactions off-chain.

Piece of code:
- [/contracts/utils/LineFactoryLib.sol#L57-L75](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L57-L75)
```
    function transferModulesToLine(address line, address spigot, address escrow) external {
        (bool success, bytes memory returnVal) = spigot.call(
          abi.encodeWithSignature("updateOwner(address)",
          address(line)
        ));
        (bool success2, bytes memory returnVal2) = escrow.call(
          abi.encodeWithSignature("updateLine(address)",
          address(line)
        ));
        (bool res) = abi.decode(returnVal, (bool));
        (bool res2) = abi.decode(returnVal2, (bool));
        if(!(success && res && success2 && res2)) {
          revert ModuleTransferFailed(line, spigot, escrow);
        }

        if(SecuredLine(payable(line)).init() != LineLib.STATUS.ACTIVE) {
          revert InitNewLineFailed(address(line), spigot, escrow);
        }
    }
```
- [/contracts/utils/LineFactoryLib.sol#L77-L88](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineFactoryLib.sol#L77-L88)
```
    function deploySecuredLine(
        address oracle, 
        address arbiter,
        address borrower, 
        address payable swapTarget,
        address s,
        address e,
        uint ttl, 
        uint8 revenueSplit
    ) external returns(address){
      return address(new SecuredLine(oracle, arbiter, borrower, swapTarget,s, e, ttl, revenueSplit));
    }
```

## Remediation
Consider emitting events for the functions mentioned above. It is also recommended to have the addresses indexed.