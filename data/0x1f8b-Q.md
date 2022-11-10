- [Low](#low)
    - [**1. Mixing and Outdated compiler**](#1-mixing-and-outdated-compiler)
    - [**2. Open TODO**](#2-open-todo)
    - [**3. Lack of address checks**](#3-lack-of-address-checks)
    - [**4. Don't reduce safety for gas savings**](#4-dont-reduce-safety-for-gas-savings)
    - [**5. Send ether in an un-compatible way**](#5-send-ether-in-an-un-compatible-way)
- [Non critical](#non-critical)
    - [**6. Add missing @param information**](#6-add-missing-param-information)
    - [**7. Wrong return**](#7-wrong-return)

# Low

## **1. Mixing and Outdated compiler**

The pragma version used are:

```
pragma solidity ^0.8.9;
pragma solidity 0.8.9;
```

*Note that mixing pragma is not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.17](https://github.com/ethereum/solidity/releases/tag/v0.8.17); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

[0.8.17](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)

- Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

Apart from these, there are several minor bug fixes and improvements.

## **2. Open TODO**

The code that contains "open todos" reflects that the development is not finished and that the code can change a posteriori, prior release, with or without audit.

**Affected source code:**

> TODO: test

- [LineFactory.sol:140](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L140)
- [LineFactory.sol:145](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L145)

## **3. Lack of address checks**

The following methods have a lack of checks if the received argument is an address, it's good practice in order to reduce human error to check that the address specified in the constructor or initialize is different than `address(0)`.

Also, the `EIP-165` standard helps detect that a smart contract implements the expected logic, prevents human error when configuring smart contract bindings, so it is recommended to check that the received argument is a contract and supports the expected interface.

**Reference:**

- https://eips.ethereum.org/EIPS/eip-165

**Affected source code:**

- [Oracle.sol:16](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/oracle/Oracle.sol#L16)
- [LineFactory.sol:26](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L26)
- [Spigot.sol:32-34](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L32-L34)
- [Escrow.sol:44-46](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/escrow/Escrow.sol#L44-L46)
- [EscrowedLine.sol:17](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L17)
- [SpigotedLine.sol:64-66](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L64-L66)
- [LineOfCredit.sol:55-57](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L55-L57)

## **4. Don't reduce safety for gas savings**

The `CreditLib.accrue` method says:

> // interest will almost always be less than deposit
> // low risk of overflow unless extremely high interest rate

This low risk is not "risk free", so it doesn't make sense to have an unchecked region in this code.

```js
  function accrue(
    ILineOfCredit.Credit memory credit,
    bytes32 id,
    address interest
  )
    public
    returns (ILineOfCredit.Credit memory)
  { unchecked {
      // interest will almost always be less than deposit
      // low risk of overflow unless extremely high interest rate

      // get token demoninated interest accrued
      uint256 accruedToken = IInterestRateCredit(interest).accrueInterest(
          id,
          credit.principal,
          credit.deposit
      );

      // update credit line balance
      credit.interestAccrued += accruedToken;

      emit InterestAccrued(id, accruedToken);
      return credit;
  } }
```

**Affected source code:**

- [CreditLib.sol:247-248](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L247-L248)

## **5. Send ether in an un-compatible way**

Because to transfer ether the `.transfer` method (which is capped at 2300 gas) is used instead of `.call` which is limited to the gas provided by the user. If a contract that has a `fallback` method more expensive than 2300 gas, creates an offer, it could be impossible for this contract to send the funds to the buyer or seller.

**Reference:**

- **transfer** -> The receiving smart contract should have a fallback function defined or else the transfer call will throw an error. There is a gas limit of 2300 gas, which is enough to complete the transfer operation. It is hardcoded to prevent reentrancy attacks.
- **send** -> It works in a similar way as to transfer call and has a gas limit of 2300 gas as well. It returns the status as a boolean.
- **call** -> It is the recommended way of sending ETH to a smart contract. The empty argument triggers the fallback function of the receiving address.

**Affected source code:**

- [LineLib.sol:48](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48)

**Recommended Mitigation Steps:**
- Use call instead.

---

# Non critical

## **6. Add missing @param information**

Some parameters have not been commented, which reflects that the logic has been updated but not the documentation, it is advisable that developers update both at the same time to avoid the code being out of sync with the project documentation.

```diff
  /**
    * see ILineOfCredit._createCredit
    * @notice called by LineOfCredit._createCredit during every repayment function
+   * @param id - Fill me
+   * @param amount - Fill me
+   * @param lender - Fill me
+   * @param token - Fill me
    * @param oracle - interset rate contract used by line that will calculate interest owed
   */
  function create(
      bytes32 id,
      uint256 amount,
      address lender,
      address token,
      address oracle
  )
```

**Affected source code:**

- [CreditLib.sol:120-131](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L120-L131)

## **7. Wrong return**

The method `removePosition` present inside the library `CreditListLib` return true when the `id` was not found, something that should be fixed in order to prevent a wrong use of this method.

**Affected source code:**

- [CreditListLib.sol:31](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L31)

