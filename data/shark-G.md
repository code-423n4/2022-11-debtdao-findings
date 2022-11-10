## Math should be unchecked when no overflowing will happen

Checked math, which is the default in ^0.8.0 expends extra gas.

Consider for example:

File: `LineOfCredit.sol` [Line 179-196](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179-L196)

```
        for (uint256 i; i < len; ++i) {
            ...
        }
```

Each time `++i` is called, overflow checks are executed. However, because `i` is already constrained by length, `i < len;`, those overflow checks are unnecessary.

Instead, the code can be rewritten as:

```
        for (uint256 i; i < len;) {
            ...
            unchecked {
                ++i;
            }
        }
```

Here are a few more examples of where this could be implemented:

File: `LineOfCredit.sol` [Line 203-210](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203-L210)
File: `LineOfCredit.sol` [Line 520-537](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520-L537)
File: `EscrowLib.sol` [Line 57-81](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L57-L81)

## `x = x + y` is more efficient than `x += y`

This is the same for subtracting.

For instance:

File: `EscrowLib.sol` [Line 96](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L96)

```
        self.deposited[token].amount += amount;
```

The above can be changed to:

```
        self.deposited[token].amount = self.deposited[token].amount + amount;
```

Here are some more instances found:

File: `EscrowLib.sol` [Line 75-79](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L75-L79)
File: `EscrowLib.sol` [Line 164](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L164)
File: `EscrowLib.sol` [Line 202](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L202)
File: `CreditLib.sol` [Line 177-178](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L177-L178)
File: `CreditLib.sol` [Line 186-187](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L186-L187)

## Unnecessary conditional statement

File: `LineOfCredit.sol` [Line 157-160](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L157-L160)

It has been commented that the logic of `_canDeclareInsolvent()` is updated in Spigoted and Escrowed lines, However, when calling internally, `_canDeclareInsolvent()` will always return true.

```
    function _canDeclareInsolvent() internal virtual returns(bool) {
        // logic updated in Spigoted and Escrowed lines
        return true;
    }
```

File: `LineOfCredit.sol` [Line 149-154](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L149-L154)

Because of this, the else statement below will never be run:

```
        if(_canDeclareInsolvent()) {
            _updateStatus(LineLib.STATUS.INSOLVENT);
            return true;
        } else {
          return false;
        }
```

The code could be refactored to:

```
            _updateStatus(LineLib.STATUS.INSOLVENT);
            return true;
```

However, `return true;` can also be omitted since it is always going to return true

## uints smaller than 256 bits can be more expensive

The EVM works with 256-bits. Every operation is based on these base units. If your data is smaller, further operations are needed to downscale from 256 bits to 8 bits. It is only more efficient when you tightly pack variables of type `uint8`, `uint16`, `uint32`, etc into the same storage slot with other adjacent variables smaller than 256 bits.

Here are some instances of this:

File: `LineFactoryLib.sol` [Line 51](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L51)
File: `SpigotedLineLib.sol` [Line 169-170](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L169-L170)

## Function Order Affects Gas Consumption

The order of the functions will have an impact on gas consumption. The reason why is that the order is dependent on the Method ID. Each function position will use up an extra 22 gas.

For more info: https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92
