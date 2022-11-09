## [G-01] abi.encode() is less efficient than abi.encodePacked()

```
File: contracts\utils\CreditLib.sol

61: function computeId()
            {
69:     return keccak256(abi.encode(line, lender, token));
            }
```


