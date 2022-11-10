## Use `uint256` instead of `uint`

To be explicit, consider replacing all instances of `uint` with `uint256`.

Here is an example:

File: `CreditLib.sol` [Line 117](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117)

```
    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```

The code above can be replaced with:

```
    return price <= 0 ? 0 : (amount * uint256(price)) / (1 * 10 ** decimals);
```

## Decimals used in constant assignment

There isn't any floating point in Solidity. Numbers should be in whole number format.

For instance:

File: `InterestRateCredit.sol` [Line 7](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L7)

```
    uint256 constant ONE_YEAR = 365.25 days;
```

The code above can be changed to:

```
    uint256 constant ONE_YEAR = 365 days + 6 hours;
```

## Unspecific Pragma Version

The compiler version `pragma solidity ^0.8.9;` is very unspecific.

Locking the pragma helps ensure that contracts don't get deployed with unintended versions, for example, the latest compiler which may have higher risks of undiscovered bugs.

## Typo mistakes

File: `CreditLib.sol` ([Line 123](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L123), [Line 200](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L200), [Line 221](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221), [Line 237](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L237), [Line 250](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L250))

```
/// @audit "interset"
123:    * @param oracle - interset rate contract used by line that will calculate interest owed

/// @audit "bwithdrawn"
200:    * @param credit - The lender position that is being bwithdrawn from

/// @audit "seeting"
221:          // emit events before seeting to 0

/// @audit "interset"
237:    * @param interest - interset rate contract used by line that will calculate interest owed

/// @audit "demoninated"
250:      // get token demoninated interest accrued
```

File: `LineFactoryLib.sol` ([Line 34](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L34))

```
/// @audit Change "based of" to "based on"
34:      @notice sets up new line based of config of old line. Old line does not need to have REPAID status for this call to succeed.
```

File: `SpigotLib.sol` ([Line 53](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L53))

```
/// @audit Add apostrophe to "doesnt"
53:         // cap so uint doesnt overflow in split calculations.
```

File: `SpigotedLineLib.sol` ([Line 44](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L44), [Line 48](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L48), [Line 188](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L188), [Line 189](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L189))

```
/// @audit "priviliged"
44:     * @dev                 priviliged internal function

/// @audit "adddress"
48:     * @param spigot        - The Spigot to claim from. Must be owned by adddress(this)

/// @audit "revenuw"
188:   * @notice -  Transfers ownership of the entire Spigot and its revenuw streams from its then Owner to either

/// @audit "been been"
189:                the Borrower (if a Line of Credit has been been fully repaid) or
```

File: `Spigot.sol` ([Line 13](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L13), [Line 133](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L133))

```
/// @audit Change "delegated" to "is delegated"
13:            - operational control of revenue generating contract belongs to Spigot's Owner and delegated to Operator.

/// @audit "revenuContract" and "paramteter"
133:     * @dev - revenuContract's transfer func MUST only accept one paramteter which is the new owner.
```

File: `EscrowedLine.sol` ([Line 45](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L45), [Line 74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L74), [Line 84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84), [Line 85](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L85))

```
/// @audit "priviliegad"
45:   * @dev priviliegad function. Do checks before calling.

/// @audit "priviliegad"
74:   *(@dev priviliegad internal function.

/// @audit "swithc" and "repyment"
84:   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment


/// @audit "priviliegad"
85:   *(@dev priviliegad internal function.

```
