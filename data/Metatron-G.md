### Upgrade pragma to 0.8.16 to save gas

Across the whole solution, the declared pragma is 0.8.9.
Upgrading to 0.8.16 has many benefits, cheaper on gas is one of them.

Just an example of how much it saves,
The following information is regarding 0.8.15 over 0.8.14.
```
According to the release note of 0.8.15: https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/
The benchmark shows saving of 2.5-10% Bytecode size,
Saving 2-8% Deployment gas,
And saving up to 6.2% Runtime gas.
```

For 0.8.16 over 0.8.9 the save is higher!