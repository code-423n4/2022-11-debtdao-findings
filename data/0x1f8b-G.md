- [Gas](#gas)
    - [**1. Use a recent solidity version**](#1-use-a-recent-solidity-version)
    - [**2. Avoid compound assignment operator in state variables**](#2-avoid-compound-assignment-operator-in-state-variables)
        - [Total gas saved: **13 * 16 = 256**](#total-gas-saved-13--16--256)
    - [**3. Remove empty blocks**](#3-remove-empty-blocks)
    - [**4. Reduce math operations**](#4-reduce-math-operations)
    - [**5. Reduce the size of error messages Long revert Strings**](#5-reduce-the-size-of-error-messages-long-revert-strings)
        - [Total gas saved: **18 * 1 = 18**](#total-gas-saved-18--1--18)
    - [**6. Use Custom Errors instead of Revert Strings to save Gas**](#6-use-custom-errors-instead-of-revert-strings-to-save-gas)
        - [Total gas saved: **9 * 24 = 216**](#total-gas-saved-9--24--216)
    - [**7. Avoid unused returns**](#7-avoid-unused-returns)
    - [**8. delete optimization**](#8-delete-optimization)
        - [Total gas saved: **5 * 1 = 5**](#total-gas-saved-5--1--5)
    - [**9. Use the unchecked keyword**](#9-use-the-unchecked-keyword)
    - [**10. Avoid index for values**](#10-avoid-index-for-values)

# Gas

## **1. Use a recent solidity version**

Using an updated version of solidity you can achieve significant gas savings, like the one mentioned above:

- With at least 0.8.10 to skip existence check for external contract if return data is expected. In this case, the ABI decoder will revert if the contract does not exist.
- With at least 0.8.16 to have a more efficient code for checked addition and subtraction.
- With at least 0.8.17 to have a more efficient overflow checks for multiplication.

## **2. Avoid compound assignment operator in state variables**

Using compound assignment operators for state variables (like `State += X` or `State -= X` ...) it's more expensive than using operator assignment (like `State = State + X` or `State = State - X` ...).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
uint256 private _a;
function testShort() public {
_a += 1;
}
}

contract TesterB {
Uint256 private _a;
function testLong() public {
_a = _a + 1;
}
}
```

Gas saving executing: **13 per entry**

```
TesterA.testShort: 43507
TesterB.testLong:  43494
```

**Affected source code:**

- [LineOfCredit.sol:276](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L276)
- [LineOfCredit.sol:351](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L351)
- [SpigotedLine.sol:122](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L122)
- [SpigotedLine.sol:125](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L125)
- [SpigotedLine.sol:144](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L144)
- [SpigotedLine.sol:172](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L172)
- [CreditLib.sol:177](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L177)
- [CreditLib.sol:178](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L178)
- [CreditLib.sol:186](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L186)
- [CreditLib.sol:187](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L187)
- [CreditLib.sol:218](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L218)
- [CreditLib.sol:227](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L227)
- [CreditLib.sol:258](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L258)
- [EscrowLib.sol:96](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L96)
- [EscrowLib.sol:164](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L164)
- [EscrowLib.sol:202](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L202)

### Total gas saved: **13 * 16 = 256**

## **3. Remove empty blocks**

An empty block or an empty method generally indicates a lack of logic that it’s unnecessary and should be eliminated, call a method that literally does nothing only consumes gas unnecessarily, if it is a `virtual` method which is expects it to be filled by the class that implements it, it is better to use `abstract`  contracts with just the definition.

**Sample code:**

```javascript
pragma solidity >=0.4.0 <0.7.0;

abstract contract BaseContract {
    function totalSupply() public virtual returns (uint256);
}
```

**Reference:**
- https://docs.soliditylang.org/en/v0.6.0/contracts.html?highlight=abstract#abstract-contracts

**Affected source code:**

- [SecuredLine.sol:13-32](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L13-L32)

## **4. Reduce math operations**

Several methods have been found in which it is possible to reduce the number of mathematical operations, the cases are detailed below.

**Affected source code:**

Use scientific notation rather than exponentiation:

- `10**5`=> `1e5` : [EscrowLib.sol:42](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42) and [EscrowLib.sol:42](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L42)


## **5. Reduce the size of error messages (Long revert Strings)**

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testShortRevert(bool path) public view {
require(path, "test error");
}
}

contract TesterB {
function testLongRevert(bool path) public view {
require(path, "test big error message, more than 32 bytes");
}
}
```

Gas saving executing: **18 per entry**

```
TesterA.testShortRevert: 21886
TesterB.testLongRevert:  21904
```

**Affected source code:**

- [InterestRateCredit.sol:26-29](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29)

### Total gas saved: **18 * 1 = 18**

## **6. Use Custom Errors instead of Revert Strings to save Gas**

Custom errors from Solidity `0.8.4` are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

**Source Custom Errors in Solidity:**

Starting from Solidity `v0.8.4`, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

**Proof of concept (*without optimizations*):**

```javascript
pragma solidity 0.8.15;

contract TesterA {
function testRevert(bool path) public view {
 require(path, "test error");
}
}

contract TesterB {
error MyError(string msg);
function testError(bool path) public view {
 if(path) revert MyError("test error");
}
}
```

Gas saving executing: **9 per entry**

```
TesterA.testRevert: 21611
TesterB.testError:  21602     
```

**Affected source code:**

- [EscrowedLine.sol:64](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L64)
- [EscrowedLine.sol:90](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L90)
- [LineOfCredit.sol:112](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L112)
- [LineOfCredit.sol:241](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L241)
- [LineOfCredit.sol:259](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L259)
- [LineOfCredit.sol:326](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L326)
- [SpigotedLine.sol:62](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L62)
- [SpigotedLine.sol:143](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L143)
- [SpigotedLine.sol:160](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L160)
- [SpigotedLine.sol:239](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L239)
- [InterestRateCredit.sol:26-29](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L26-L29)
- [EscrowLib.sol:91](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L91)
- [EscrowLib.sol:105](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L105)
- [EscrowLib.sol:161](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L161)
- [EscrowLib.sol:198](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L198)
- [EscrowLib.sol:216](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L216)
- [SpigotLib.sol:96](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L96)
- [SpigotLib.sol:128](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L128)
- [SpigotLib.sol:130](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L130)
- [SpigotLib.sol:155](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L155)
- [SpigotLib.sol:180](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L180)
- [SpigotLib.sol:189](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L189)
- [SpigotLib.sol:201](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L201)
- [SpigotedLineLib.sol:147](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L147)

### Total gas saved: **9 * 24 = 216**

## **7. Avoid unused returns**

Having a method that always returns the same value is not correct in terms of consumption, if you want to modify a value, and the method will perform a `revert` in case of failure, it is not necessary to return a `true`, since it will never be `false`. It is less expensive not to return anything, and it also eliminates the need to double-check the returned value by the caller.

**Affected source code:**

- [LineLib.sol:34-51](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L34-L51)
- [LineLib.sol:59-74](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L59-L74)
- [EscrowedLine.sol:77-80](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L77-L80)
- [EscrowedLine.sol:89-92](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/EscrowedLine.sol#L89-L92)
- [LineOfCredit.sol:157-160](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L157-L160)
- [LineOfCredit.sol:200-210](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L200-L210)
- [LineOfCredit.sol:247-262](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L247-L262)
- [LineOfCredit.sol:265-285](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L265-L285)
- [LineOfCredit.sol:292-311](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L292-L311)
- [LineOfCredit.sol:315-333](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L315-L333)
- [LineOfCredit.sol:340-367](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L340-L367)
- [LineOfCredit.sol:370-385](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L370-L385)
- [LineOfCredit.sol:388-409](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L388-L409)
- [LineOfCredit.sol:483-507](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L483-L507)
- [SecuredLine.sol:48-66](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SecuredLine.sol#L48-L66)
- [SpigotedLine.sol:137-151](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L137-L151)
- [InterestRateCredit.sol:74-86](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L86)
- [CreditListLib.sol:20-32](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L20-L32)
- [CreditListLib.sol:40-60](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L40-L60)
- [EscrowLib.sol:104-149](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L104-L149)
- [EscrowLib.sol:192-207](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L192-L207)
- [EscrowLib.sol:215-219](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L215-L219)
- [SpigotLib.sol:61-80](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L61-L80)
- [SpigotLib.sol:125-140](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L125-L140)
- [SpigotLib.sol:143-161](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L143-L161)
- [SpigotLib.sol:164-175](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L164-L175)
- [SpigotLib.sol:178-184](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L178-L184)
- [SpigotLib.sol:187-193](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L187-L193)
- [SpigotLib.sol:196-205](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L196-L205)
- [SpigotLib.sol:208-213](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L208-L213)
- [SpigotedLineLib.sol:120-140](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L120-L140)
- [SpigotedLineLib.sol:146-149](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L146-L149)
- [SpigotedLineLib.sol:151-160](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L151-L160)

## **8. `delete` optimization**

Use `delete` instead of set to default value (`false` or `0`).

5 gas could be saved per entry in the following affected lines:

**Affected source code:**

- [SpigotLib.sol:117](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L117)

### Total gas saved: **5 * 1 = 5**

## **9. Use the `unchecked` keyword**

When an underflow or overflow cannot occur, one might conserve gas by using the `unchecked` keyword to prevent unnecessary arithmetic underflow/overflow tests.

**Affected source code:**

- [LineOfCredit.sol:179](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L179)
- [LineOfCredit.sol:203](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L203)
- [LineOfCredit.sol:520](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L520)
- [CreditListLib.sol:23](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L23)
- [CreditListLib.sol:51](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditListLib.sol#L51)
- [EscrowLib.sol:57](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L57)

## **10. Avoid index for values**

There is no need to create index for amount or values, these values will be different each time, and there are no reason to have an index on them.

```js
    event AddCredit(
        address indexed lender,
        address indexed token,
        uint256 indexed deposit,
        bytes32 id
    );

  /// @notice Emits data re Lender removes funds (principal) - there is no corresponding function, just withdraw()
  event WithdrawDeposit(bytes32 indexed id, uint256 indexed amount);
  
  /// @notice Emits data re Lender withdraws interest - there is no corresponding function, just withdraw()
  // Bob - consider changing event name to WithdrawInterest
  event WithdrawProfit(bytes32 indexed id, uint256 indexed amount);
  

  /// @notice After accrueInterest runs, emits the amount of interest added to a Borrower's outstanding balance of interest due 
  // but not yet repaid to the Line of Credit contract
  event InterestAccrued(bytes32 indexed id, uint256 indexed amount);


  // Borrower Events

  event Borrow(bytes32 indexed id, uint256 indexed amount);
  // Emits notice that a Borrower has drawn down an amount on a credit line

  event RepayInterest(bytes32 indexed id, uint256 indexed amount);
  /** Emits that a Borrower has repaid an amount of interest 
  (N.B. results in an increase in interestRepaid, i.e. interest not yet withdrawn by a Lender). There is no corresponding function
  */
  
  event RepayPrincipal(bytes32 indexed id, uint256 indexed amount);
  // Emits that a Borrower has repaid an amount of principal - there is no corresponding function
```

**Affected source code:**

- [CreditLib.sol:16-48](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L16-L48)
