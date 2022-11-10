# QA

## [L-01] Missing `address(0)` check in `LineFactory` constructor

[LineFactory.sol#L20-L39](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L20-L39)

Change this:

```solidity
    constructor(
        address moduleFactory,
        address arbiter_,
        address oracle_,
        address swapTarget_
    ) {
        factory = IModuleFactory(moduleFactory);
        if (arbiter_ == address(0)) {
            revert InvalidArbiterAddress();
        }
        if (oracle_ == address(0)) {
            revert InvalidOracleAddress();
        }
        if (swapTarget_ == address(0)) {
            revert InvalidSwapTargetAddress();
        }
        arbiter = arbiter_;
        oracle = oracle_;
        swapTarget = swapTarget_;
    }
```

To this:

```solidity
    constructor(
        address moduleFactory_,
        address arbiter_,
        address oracle_,
        address swapTarget_
    ) {
        if (moduleFactory_ == address(0)) {
            revert InvalidModuleFactoryAddress();
        }
        if (arbiter_ == address(0)) {
            revert InvalidArbiterAddress();
        }
        if (oracle_ == address(0)) {
            revert InvalidOracleAddress();
        }
        if (swapTarget_ == address(0)) {
            revert InvalidSwapTargetAddress();
        }
        factory = IModuleFactory(moduleFactory_);
        arbiter = arbiter_;
        oracle = oracle_;
        swapTarget = swapTarget_;
    }
```