If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. ```require(msg.sender == address(weth))```). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

There is 1 instance of this issue:
```
File: contracts/modules/credit/SpigotedLine.sol

272:     receive() external payable {}

```

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/modules/credit/SpigotedLine.sol#L272