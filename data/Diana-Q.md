
## L-01  NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

There are multiple instances of this issue

Some instances include:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

1: pragma solidity ^0.8.9;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol

```
File: contracts/modules/credit/SecuredLine.sol

1: pragma solidity ^0.8.9;
```

### Recommended Mitigation Steps

Lock the pragma version

----------

## L-02  RACE CONDITION IN approve()

### Proof of Concept

_There is 1 instance of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol

```
File: contracts/utils/SpigotedLineLib.sol

134: IERC20(sellToken).approve(swapTarget, amount);
```

### Recommended Mitigation Steps

Add safeincreaseAllowance and safedecreaseAllowance methods in ERC20 contract

-----------

## N-01 EVENT IS MISSING INDEXED FIELDS

Each `event` should use three `indexed` fields if there are three or more fields

_There are 3 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol

```
File: contracts/utils/SpigotLib.sol

241-244: event AddSpigot(
        address indexed revenueContract,
        uint256 ownerSplit
    );
255-260: event ClaimRevenue(
        address indexed token,
        uint256 indexed amount,
        uint256 escrowed,
        address revenueContract
    );
262-266: event ClaimEscrow(
        address indexed token,
        uint256 indexed amount,
        address owner
    );
```

----

## N-02 USE OF block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

_There are 5 instances of this issue_

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol

```
File: contracts/modules/credit/LineOfCredit.sol

58: deadline = block.timestamp + ttl_;
132: if (block.timestamp >= deadline && count > 0) {
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol

```
File: contracts/modules/interest-rate/InterestRateCredit.sol

48: uint256 timespan = block.timestamp - rate.lastAccrued;
50: rates[id].lastAccrued = block.timestamp;
82: lastAccrued: block.timestamp
```

--------

## N-03 USE A MORE RECENT VERSION OF SOLIDITY

It's a best practice to use the latest compiler version.

The specified minimum compiler version is quite old. Older compilers might be susceptible to some bugs. We recommend changing the solidity version pragma to the latest version to enforce the use of an up to date compiler.

List of known compiler bugs and their severity can be found here: [https://etherscan.io/solcbuginfo](https://etherscan.io/solcbuginfo)

There are multiple instances of this issue

Some instances include:

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol

```
File: contracts/modules/credit/EscrowedLine.sol

1: pragma solidity 0.8.9;
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol

```
File: contracts/utils/Spigot.sol

1: pragma solidity 0.8.9;
```

--------------

