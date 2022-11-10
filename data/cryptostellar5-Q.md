### L-01 `safeIncreaseAllowance` AND `safeDecreaseAllowance` INSTEAD OF `approve()` 

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L134

```
134: IERC20(sellToken).approve(swapTarget, amount);
```

- approve should be avoided as it can be front run
-   `safeApprove()` has been deprecated in favour of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`
-   using `approve()` might fail because some tokens (eg. USDT) don’t work when changing the allowance from an existing non-zero allowance value

### Recommended Mitigation Steps

Update instances of `approve()` and `safeApprove()` to `safeIncreaseAllowance()`.


### L-02 MISSING EVENTS FOR IMPORTANT FUNCTIONS/MODIFIERS

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L25-L31

```
  modifier onlyLineContract() {
        require(
            msg.sender == lineContract,
            "InterestRateCred: only line contract."
        );
        _;
    } 
```



### L-03 FLOATING PRAGMA VERSION SHOULD NOT BE USED

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

This is applicable to all the contracts


```
pragma solidity ^0.8.9;
```

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)


### N-01 EVENTS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol

```
21: event MutualConsentRegistered(
22:        bytes32 _consentHash
23:     );
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
241: event AddSpigot(address indexed revenueContract, uint256 ownerSplit);
255: event ClaimRevenue(address indexed token,uint256 indexed amount,uint256 escrowed,    address revenueContract);
262: event ClaimEscrow(address indexed token,uint256 indexed amount,address owner);
```


### N-02 REQUIRE() STATEMENTS SHOULD HAVE DESCRIPTIVE REASON STRINGS

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol

```
64: require(escrow_.liquidate(amount, targetToken, to));
90: require(escrow.updateLine(newLine));
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol

```
143: require(amount <= unusedTokens[credit.token]);
160: require(msg.sender == borrower);
239: require(msg.sender == arbiter);
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol

```
91: require(amount > 0);
105: require(msg.sender == ILineOfCredit(self.line).arbiter());
161: require(amount > 0);
198: require(amount > 0);
216: require(msg.sender == self.line);
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol

```
147: require(ISpigot(spigot).updateOwner(newLine));
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
96: require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
130: require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
155: require(success);
180: require(newOwner != address(0));
189: require(newOwner != address(0));
201: require(newTreasury != address(0));
```



### N-03  NATSPEC IS INCOMPLETE

Missing @params in Natspec

For example: https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L146-L151

```
function updateOwnerSplit(address revenueContract, uint8 ownerSplit) 
        external
        returns(bool)
    {
      return state.updateOwnerSplit(revenueContract, ownerSplit);
    }
```


### N-04 USE LATEST SOLIDITY VERSION

When deploying contracts, you should use the latest released version of Solidity. Apart from exceptional cases, only the latest version receives [security fixes](https://github.com/ethereum/solidity/security/policy#supported-versions). Furthermore, breaking changes as well as new features are introduced regularly.
Latest Version is 0.8.17

This is applicable to all the smart contracts.
