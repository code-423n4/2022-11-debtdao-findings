************G-01:************ Pre-compute all constants (for example `INTEREST_DENOMINATOR`) to save gas

************G-02:************ Replace all require statements with Solidity Custom Errors to save gas

************G-03:************ Use `x = x + y` and `x = x - y` instead of `x += y` and `x -= y` to save gas

****************G-04:**************** Always wrap `++i` in `unchecked{}` when it can’t overflow (in all for loops in code)

************G-05:************ Use `uint256` instead of `uint8` for constants, since smaller uint types need casting and waste gas

************G-06:************ Add `indexed` keyword to at least 3 parameters in all events to save gas

************G-07:************ No need to ever do `1 * ...` (`CreditLib::calculateValue`)