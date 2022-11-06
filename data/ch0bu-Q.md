# LOW

## 1. Unused/empty receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

```
272	receive() external payable {}
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol


## 2. Immutable addresses lack zero-address check

Constructors should check the address written in an immutable address variable is not the zero address.

```
56	arbiter = arbiter_;
57	borrower = borrower_;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol
```
66	swapTarget = swapTarget_;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol
```
49	oracle = _oracle;
50	borrower = _borrower;
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol


# NON-CRITICAL

## 1. Non-library/interface files should use fixed compiler versions, not floating ones

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol


## 2. Use a more recent version of Solidity

- Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`
- Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

```
All contracts use Solidity 0.8.9 version
```


## 3. File does not contain an SPDX Identifier

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol


## 4. Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)

Scientific notation should be used for better code readability

```
42	uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol


## 5. The `nonReentrant modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

```
93	function claimAndRepay(address claimToken, bytes calldata zeroExTradeData)
94		external
95		whileBorrowing
96		nonReentrant
97		returns (uint256)
98	{
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol


## 6. NatSpec is incomplete

Check here for NatSpec rules:
https://docs.soliditylang.org/en/develop/natspec-format.html

```
Most of the contracts are missing @title and @author tags
A lot of functions are missing @param and @return tags that explain input/output variables
```


## 7. Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```
241	event AddSpigot(
242		address indexed revenueContract,
243		uint256 ownerSplit
...
255	event ClaimRevenue(
256		address indexed token,
257		uint256 indexed amount,
258		uint256 escrowed,
259		address revenueContract
...
262	event ClaimEscrow(
263		address indexed token,
264		uint256 indexed amount,
265		address owner
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

