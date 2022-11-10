# Using payable(address).transfer to send ETH out

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/LineLib.sol#L48

```solidity
            payable(receiver).transfer(amount);
```

#### Vulnerability detail and recommended fix

transfer() uses a fixed amount of gas, which can result in revert.
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

Use call instead of transfer(). Example:
(bool succeeded, ) = _to.call{value: _amount}("");
require(succeeded, "Transfer failed.");

# miss address(0) check when setting crucial state is set

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L49

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L42

#### Vulnerability detail and recommended fix

In LineOfCredit constructor.sol, we should verify if the oracle, arbiter and borrower address are set to address(0)

```solidity
constructor(
	address oracle_,
	address arbiter_,
	address borrower_,
	uint256 ttl_
) {
	oracle = IOracle(oracle_);
	arbiter = arbiter_;
	borrower = borrower_;
	deadline = block.timestamp + ttl_;  //the deadline is the term/maturity/expiry date of the Line of Credit facility
	interestRate = new InterestRateCredit();

	emit DeployLine(oracle_, arbiter_, borrower_);
}
```

In Escrow.sol contract, we should verify if the address oracle, line and borrower are set to address(0)

```solidity
constructor(
	uint32 _minimumCollateralRatio,
	address _oracle,
	address _line,
	address _borrower
) {
	minimumCollateralRatio = _minimumCollateralRatio;
	oracle = _oracle;
	borrower = _borrower;
	state.line = _line;
}
```

# Unsafe use of the approve.

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L134

#### Vulnerability detail and recommended fix

In SpigotedLineLib.sol, it is not safe to call approve directly

```solidity
IERC20(sellToken).approve(swapTarget, amount);
```

Some tokens (e.g. USDT, KNC) do not allow approving an amount M > 0 when an existing amount N > 0 is already approved.

We recommend the project use Openzepplein SafeApprove library to avoid this issue. We also recommend the project consider use safeIncreaseAllowance to avoid the approve race condition.

# Better naming in LineOfCredit.sol#counts()

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L117

#### Vulnerability detail and recommended fix

the function counts() in LineOfCredit returns amount of active ids and the total ids in list

```solidity
  /** 
    * @notice - getter for amount of active ids + total ids in list
    * @return - (uint, uint) - active credit lines, total length
  */
  function counts() external view returns (uint256, uint256);
```

the implementation is

```solidity
/// see ILineOfCredit.counts
function counts() external view returns (uint256, uint256) {
	return (count, ids.length);
}
```

However, the naming can be better to indicate the returned value is amount of active ids + total ids in list,

we recommend the project change the name to

```solidity
function creditLineInfo() external view returns (uint256, uint256) {
	return (numberOfActiveCreditLine, creditLineArray.length);
}
```


# Require statement does not error message.

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L91


```solidity
        require(amount > 0);
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L105


```solidity
        require(msg.sender == ILineOfCredit(self.line).arbiter());
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L161


```solidity
        require(amount > 0);
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L198


```solidity
        require(amount > 0);
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/EscrowLib.sol#L216


```solidity
      require(msg.sender == self.line);
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L128


```solidity
        require(revenueContract != address(this));
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L130


```solidity
        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L155


```solidity
        require(success);
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L180


```solidity
        require(newOwner != address(0));
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L189


```solidity
        require(newOperator != address(0));
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotLib.sol#L201


```solidity
        require(newTreasury != address(0));
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/utils/SpigotedLineLib.sol#L147


```solidity
      require(ISpigot(spigot).updateOwner(newLine));
```

#### Vulnerability detail and recommended fix

Descriptive reason strings should be used so that users can troubleshot any reverted calls

# floating pragma soliidty version

### Line Of Code

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/interfaces/ISpigotedLine.sol#L1


```solidity
pragma solidity ^0.8.9;
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/interfaces/ISpigot.sol#L1


```solidity
pragma solidity ^0.8.9;
```
            

https://github.com/debtdao/Line-of-Credit/blob/6987988fe39901cad9a8e5ebb2c6aa719590873d/contracts/interfaces/IInterestRateCredit.sol#L1


```solidity
pragma solidity ^0.8.9;
```

#### Vulnerability detail and recommended fix

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.