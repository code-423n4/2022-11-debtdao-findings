## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logic that depends strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future. Here are some instances found.

[Line 132](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132)

```
        if (block.timestamp >= deadline && count > 0) {
```
[Lines 48 - 52](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L48-L50)

```
        uint256 timespan = block.timestamp - rate.lastAccrued;
        // update last timestamp in storage
        rates[id].lastAccrued = block.timestamp;
```
[Line 82](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L82)

```
            lastAccrued: block.timestamp
```

## COMMENT AND CODE LINE LENGTH
Try limit the length of comments and/or code lines to 80 - 100 character long for readability sake. Here are some instances found.

[Line 12](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditListLib.sol#L12)

```
     * @dev assumes that `id` of a single credit line within the Line of Credit facility (same lender/token) is stored only once in the `positions` array
```
[Lines 36 - 37](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L36-L37)

```
     * @notice - excess unsold revenue claimed from Spigot to be sold later or excess credit tokens bought from revenue but not yet used to repay debt
     *         - needed because the Line of Credit might have the same token being lent/borrower as being bought/sold so need to separate accounting. 
```
[Line 71](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71)

```
    * @dev    - Used if we setup Escrow before Line exists. Line has no way to interface with this function so once transfered `line` is set forever
```

## TIMELOCK FOR CRITICAL PARAMETER CHANGE
It is a good practice giving time to users to react and adjust to critical changes with a mandatory time window between the changes. The first step is simply broadcasting to users with a specific change that is coming whilst the second step commits that change after an appropriate period of waiting. This would allow time for users opposing to the change to withdraw within the set time frame. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of the owner making a malicious act). Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes. Here are the instances found.

[Lines 125 - 213](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125-L213)

```
    function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }

        require(revenueContract != address(this));
        // spigot setting already exists
        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));
        
        // must set transfer func
        if(setting.transferOwnerFunction == bytes4(0)) { revert BadSetting(); }
        if(setting.ownerSplit > MAX_SPLIT) { revert BadSetting(); }
        
        self.settings[revenueContract] = setting;
        emit AddSpigot(revenueContract, setting.ownerSplit);

        return true;
    }

    /** see Spigot.removeSpigot */
    function removeSpigot(SpigotState storage self, address revenueContract)
        external
        returns (bool)
    {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }

        (bool success,) = revenueContract.call(
            abi.encodeWithSelector(
                self.settings[revenueContract].transferOwnerFunction,
                self.operator    // assume function only takes one param that is new owner address
            )
        );
        require(success);

        delete self.settings[revenueContract];
        emit RemoveSpigot(revenueContract);

        return true;
    }

    /** see Spigot.updateOwnerSplit */
    function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
        external
        returns(bool)
    {
      if(msg.sender != self.owner) { revert CallerAccessDenied(); }
      if(ownerSplit > MAX_SPLIT) { revert BadSetting(); }

      self.settings[revenueContract].ownerSplit = ownerSplit;
      emit UpdateOwnerSplit(revenueContract, ownerSplit);
      
      return true;
    }

    /** see Spigot.updateOwner */
    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }
        require(newOwner != address(0));
        self.owner = newOwner;
        emit UpdateOwner(newOwner);
        return true;
    }

    /** see Spigot.updateOperator */
    function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
        if(msg.sender != self.operator) { revert CallerAccessDenied(); }
        require(newOperator != address(0));
        self.operator = newOperator;
        emit UpdateOperator(newOperator);
        return true;
    }

    /** see Spigot.updateTreasury */
    function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {
        if(msg.sender != self.operator && msg.sender != self.treasury) {
          revert CallerAccessDenied();
        }

        require(newTreasury != address(0));
        self.treasury = newTreasury;
        emit UpdateTreasury(newTreasury);
        return true;
    }

    /** see Spigot.updateWhitelistedFunction*/
    function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }
        self.whitelistedFunctions[func] = allowed;
        emit UpdateWhitelistFunction(func, allowed);
        return true;
    }
```

## TODO
Open TODO can point to an architecture or programming issue needing to be resolved. It is recommended resolving them before deploying.

Here are some of the instances found:

[Lines 140 - 148](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140-L148)

```
        // TODO: test
        if (mSpigot == address(0)) {
            revert InvalidSpigotAddress();
        }

        // TODO: test
        if (mEscrow == address(0)) {
            revert InvalidEscrowAddress();
        }
```

## MISSING NATSPEC
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.16/natspec-format.html

It is recommended using a special form of comments, i.e., the Ethereum Natural Language Specification Format (NatSpec) to provide rich documentation for functions, return variables and more.

Here are some instances found.

[Lines 64 - 91](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L64-L91)

```
    function init() external virtual returns(LineLib.STATUS) {
      if(status != LineLib.STATUS.UNINITIALIZED) { revert AlreadyInitialized(); }
      return _updateStatus(_init());
    }

    function _init() internal virtual returns(LineLib.STATUS) {
       // If no collateral or Spigot then Line of Credit is immediately active
      return LineLib.STATUS.ACTIVE;
    }

    ///////////////
    // MODIFIERS //
    ///////////////

    modifier whileActive() {
        if(status != LineLib.STATUS.ACTIVE) { revert NotActive(); }
        _;
    }

    modifier whileBorrowing() {
        if(count == 0 || credits[ids[0]].principal == 0) { revert NotBorrowing(); }
        _;
    }

    modifier onlyBorrower() {
        if(msg.sender != borrower) { revert CallerAccessDenied(); }
        _;
    }
```
[Lines 223 - 285](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L222-L285)

```
    /// see ILineOfCredit.addCredit
    function addCredit(
        uint128 drate,
        uint128 frate,
        uint256 amount,
        address token,
        address lender
    )
        external
        payable
        override
        whileActive
        mutualConsent(lender, borrower)
        returns (bytes32)
    {
        LineLib.receiveTokenOrETH(token, lender, amount);

        bytes32 id = _createCredit(lender, token, amount);

        require(interestRate.setRate(id, drate, frate));
        
        return id;
    }

    /// see ILineOfCredit.setRates
    function setRates(
        bytes32 id,
        uint128 drate,
        uint128 frate
    )
      external
      override
      mutualConsentById(id)
      returns (bool)
    {
        Credit memory credit = credits[id];
        credits[id] = _accrue(credit, id);
        require(interestRate.setRate(id, drate, frate));
        emit SetRates(id, drate, frate);
        return true;
    }

    /// see ILineOfCredit.increaseCredit
    function increaseCredit(bytes32 id, uint256 amount)
      external
      payable
      override
      whileActive
      mutualConsentById(id)
      returns (bool)
    {
        Credit memory credit = credits[id];
        credit = _accrue(credit, id);

        credit.deposit += amount;
        
        credits[id] = credit;

        LineLib.receiveTokenOrETH(credit.token, credit.lender, amount);

        emit IncreaseCredit(id, amount);

        return true;
    }
```
[Lines 21 - 29](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L21-L29)

```
    uint256 public immutable deadline;

    address public immutable borrower;

    address public immutable arbiter;

    IOracle public immutable oracle;

    InterestRateCredit public immutable interestRate;
```

## SANITY CHECKS
Zero address and zero value checks implemented at the constructor could avoid human errors leading to non-functional calls associated with the mistakes. This is especially so when the incidents entail immutable variables preventing them from getting reassigned that could end up having the protocol redeploy the contract.

## USE OF DESCRIPTIVE VARIABLE NAMES
Non-descriptive local variables could make code base difficult to read and navigate. Here are some of the instance found.

[Line 390](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L390)

```
        address b = borrower; // gas savings
```
[Lines 123 - 128](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L123-L129)

```
        LineLib.STATUS s = status;
        if (
            s == LineLib.STATUS.REPAID ||               // end state - good
            s == LineLib.STATUS.INSOLVENT               // end state - bad
        ) {
            return s;
        }
```
[Lines 48 -53](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L48-L53)

```
        address s = address(SecuredLine(oldLine).spigot());
        address e = address(SecuredLine(oldLine).escrow());
        address payable st = SecuredLine(oldLine).swapTarget();
        uint8 split = SecuredLine(oldLine).defaultRevenueSplit();
        SecuredLine line = new SecuredLine(oracle, arbiter, borrower, st, s, e, ttl, split);
        emit DeployedSecuredLine(address(line), s, e, st, split);
```

## DOS ON UNBOUNDED LOOP
Unbounded loop could lead to OOG (Out of Gas) denying the users' of needed services. Here are some instances found.

[Line 179](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L179)

```
        for (uint256 i; i < len; ++i) {
```
[Line 203](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203)

```
        for (uint256 i; i < len; ++i) {
```
[Line 520](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L520)

```
        for (uint256 i; i <= lastSpot; ++i) {
```

## TYPO ERRORS
[Line 123](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L123)

```
    @ interset should be corrected to interest
    * @param oracle - interset rate contract used by line that will calculate interest owed
```

[Line 84](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84)

```
    @ swithc and repyment should respectively be corrected to switch and repayment
   * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment
```

[Line 221](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221)

```
    @ seeting should be corrected setting
          // emit events before seeting to 0
```
## COMPILER VERSION PRAGMA SPECIFICITY
Non-library contracts and interfaces should avoid using floating pragmas ^0.8.9. Doing this may be a security risk for the actual application implementation itself. For instance, a known vulnerable compiler version may accidentally be selected or a security tool might fallback to an older compiler version leading to checking a different EVM compilation that is ultimately deployed on the blockchain.

## AVOID THE USE OF DECIMALS
Numbers should be kept in numerical whole format since Solidity does not feature floating point.

As documented in the link below:

https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html

The following instance of constant should be assigned in an error free manner utilizing as much of the time units as possible:

[Line 7](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L7)

```
    uint256 constant ONE_YEAR = 365 days + 6 hours;
```
## EMPTY RECEIVE() FUNCTION
If the intention is for the ETH to be used, the function should call another function, otherwise it should revert via something like `require(msg.sender == address(weth))`.

Here is one instance found.

[Line 272](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272)

```
    receive() external payable {}
```
## REQUIRE STATEMENT WITH MISSING ERROR MESSAGE
Consider adding a relevant message to each of the require statements so that it would be displayed upon reverting. It is generally not implemented throughout all code bases associated with the contracts in scope.

## MISSING EVENTS ON CRITICAL OPERATIONS
Several critical operations do not trigger events, which will make it difficult to review the correct behavior of the contracts once deployed. Users and blockchain monitoring systems will not be able to easily detect suspicious behaviors without events. Here is one of the instances found.

[Lines 74 - 86](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L74-L86)

```
    function setRate(
        bytes32 id,
        uint128 dRate,
        uint128 fRate
    ) external onlyLineContract returns (bool) {
        rates[id] = Rate({
            dRate: dRate,
            fRate: fRate,
            lastAccrued: block.timestamp
        });

        return true;
    }
```
## SETTING TO DEFAULT VALUES
As documented in the link:

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=delete#delete

It is recommended using `delete` whenever there is a need to set state variable to its default value, which has a good side effect of saving gas. Here are the instances found.

[Line 188](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L188)

```
          credit.interestAccrued = 0;
```

[Line 117](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L117)

```
        self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?
```
## PAYABLE VISIBILITY IS MISSING
As commented on [line 54](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L54), `receiveTokenOrETH()` is intended to receive ETH or ERC20 token from an external contract. However, with `payable` visibility missing, all calls to send in ETH to the contract are expected to fail.

[Lines 59 - 74](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L59-L74)

```
    function receiveTokenOrETH(
      address token,
      address sender,
      uint256 amount
    )
      external
      returns (bool)
    {
        if(token == address(0)) { revert TransferFailed(); }
        if(token != Denominations.ETH) { // ERC20
            IERC20(token).safeTransferFrom(sender, address(this), amount);
        } else { // ETH
            if(msg.value < amount) { revert TransferFailed(); }
        }
        return true;
    }
```
## PAYABLE(<ADDRESS>).TRANSFER COULD CAUSE ETHER UNABLE TO SEND
When sending ETH, `sendOutTokenOrETH()` in `LineLib.sol` uses Solidity’s transfer function. This has some notable shortcomings when the address is a smart contract, which can render ETH impossible to withdraw. Specifically, the withdrawal will inevitably fail when: 1) The withdrawer smart contract does not implement a payable fallback function. 2) The withdrawer smart contract implements a payable fallback function which uses more than 2300 gas units. 3) The withdrawer smart contract implements a payable fallback function which needs less than 2300 gas units but is called through a proxy that raises the call’s gas usage above 2300. Here is an instance found.

[Lines 34 - 51]

```
    function sendOutTokenOrETH(
      address token,
      address receiver,
      uint256 amount
    )
      external
      returns (bool)
    {
        if(token == address(0)) { revert TransferFailed(); }
        
        // both branches revert if call failed
        if(token!= Denominations.ETH) { // ERC20
            IERC20(token).safeTransfer(receiver, amount);
        } else { // ETH
            payable(receiver).transfer(amount);
        }
        return true;
    }
```
It is recommended that `sendValue` function available in OpenZeppelin Contract’s Address library can be used to transfer the withdrawn Ether without being limited to 2300 gas units. Alternatively, a low level `call()` may also be adopted. Risks of re-entrancy stemming from the use of this function can be mitigated by tightly following the “Check-effects-interactions” pattern and using OpenZeppelin Contract’s ReentrancyGuard contract.

## USE UINT256 INSTEAD OF 'UINT`
For explicitness reason, it is recommended replacing all instances of `uint` with `uint256`. Here is one instance of `uint` used in the code base. 

[Line 117](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117)

```
    return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);
```
## SINGLE POINT OF FAILURE
Contract owner possesses numerous privileges that could prove disastrous if the private key was compromised. If the key was lost/unrecoverable, all needed system parameter updates would be halted.

An established owner is generally prone to target attack, especially given the insufficient protection on sensitive owner private keys. The concentration of privileges creates a single point of failure, leading to transfer of ownership and malicious reset of setter function parameters.

The following measures are recommended.
1) Split privileges using multisig option making sure that no one address has excessive ownership of the system.
2) Clearly document the functions and implementations the owner can change.
3) Document the risks associated with privileged users and single points of failure.
4) Make sure that users are aware of all the risks associated with the system.

## NEW AND OLD VALUES SHOULD BE EMITTED
It is recommended having events associated with setter functions emit both the new and old values instead of just the new value. Here are some of the instances found.

[Lines 178 - 205](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178-L205)

```
    function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
        if(msg.sender != self.owner) { revert CallerAccessDenied(); }
        require(newOwner != address(0));
        self.owner = newOwner;
        emit UpdateOwner(newOwner);
        return true;
    }

    /** see Spigot.updateOperator */
    function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
        if(msg.sender != self.operator) { revert CallerAccessDenied(); }
        require(newOperator != address(0));
        self.operator = newOperator;
        emit UpdateOperator(newOperator);
        return true;
    }

    /** see Spigot.updateTreasury */
    function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {
        if(msg.sender != self.operator && msg.sender != self.treasury) {
          revert CallerAccessDenied();
        }

        require(newTreasury != address(0));
        self.treasury = newTreasury;
        emit UpdateTreasury(newTreasury);
        return true;
    }
```
Note: A two step change should also be implemented to the above transfer of privileged roles by first assigning role to an address in a pending state. And then, `acceptOwner()`, `updateOperator()` and `updateTreasury()` should be introduced for the newly nominated assignee to respectively claim the new role. This will ensure the new assignee is going to be fully aware of the privilege assigned/transferred. Additionally, the risk of having a privileged role transferred to an invalid address will be avoided. 

## CONTRACT EXISTENCE CHECK
Low-level calls do not check for contract existence. They are known to return success even though no function call has been executed when involving contract that may not have been deployed or have been self-destructed. Here is one of the instances found.

[Line 131](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L131)

```
            (bool success, ) = swapTarget.call{value: amount}(zeroExTradeData);
```