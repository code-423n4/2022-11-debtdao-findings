## Missing checks for `address(0x0)` when assigning values to `address` state variables

Missing checks for zero-addresses may lead to infunctional protocol, if the variable addresses are updated incorrectly.

```
        registry = FeedRegistryInterface(_registry);
```

>File: contracts/modules/oracle/Oracle.sol [(line 16)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L16)

```
        registry = FeedRegistryInterface(_registry);
```

>File: contracts/modules/factories/LineFactory.sol [(line 36)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L36)

```
Other instances of this issue are :
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L37
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L38
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L36-L38
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L48-L51
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L17
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L55-L57
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L13-L29
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L53-L58

## Unused `receive()` function will lock Ether in contract
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
    receive() external payable {
        return;
    }
```

>File: contracts/modules/spigot/Spigot.sol [(line 227-229)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L227-L229)

```
       receive() external payable {}
```

>File: contracts/modules/credit/SpigotedLine.sol [(line 272)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L272)

## Upgradeable contract is missing a `__gap[50]` storage variable to allow for new storage variables in later versions

While some contracts may not currently be sub-classed, adding the variable now protects against forgetting to add it in the future.

```
	contract Spigot is ISpigot, ReentrancyGuard {
```
>File: contracts/modules/spigot/Spigot.sol [(line 16)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L16)

```
	contract LineOfCredit is ILineOfCredit, MutualConsent {
```
>File: contracts/modules/credit/LineOfCredit.sol [(line 16)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L16)

```
Other instances of this issue are :
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L11
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L22

## open `TODO` comments

Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.

```
        // TODO: test
```
* File: contracts/modules/factories/LineFactory.sol [(line 140)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L140)

```
        // TODO: test
```
* File: contracts/modules/factories/LineFactory.sol [(line 145)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L145)

## TYPOS
```
     ///@audit: `priviliged` 
     * @dev     - priviliged internal function
```
* File: contracts/modules/credit/SpigotedLine.sol [(line 180)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L180)


```
     ///@audit: `priviliged` 
     * @dev     - priviliged internal function
```
* File: contracts/modules/credit/SpigotedLine.sol [(line 179)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L179)

```
Other instances of this issue are :
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L84 (tran / itself)	///@audit: `transfered` & `itselgf`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L46	///@audit:`priviliges ` 
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L38	///@audit:`interfer `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L110	///@audit: `fuly `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L510	///@audit:`availble`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L416	///@audit:`diferent `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L395	///@audit:`prinicpal`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L107	///@audit:`diferent `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L45	///@audit:`priviliges `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L37	///@audit:`accross `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84	///@audit:`swithc ` & `repyment`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L133	///@audit:`paramteter `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L44	///@audit:`priviliged `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L48	///@audit:`adddress`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L200	///@audit:`bwithdrawn `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L221	///@audit:`seeting `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L71	///@audit:`transfered `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L94	///@audit:`thdeposits `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L45	///@audit:`priviliegad `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L74	///@audit:`priviliegad `
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L85	///@audit:`priviliegad`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84	///@audit:`repyment` && `swithc`


## Use of `block.timestamp`

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers, locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
        uint256 timespan = block.timestamp - rate.lastAccrued;
```
>File: contracts/modules/interest-rate/InterestRateCredit.sol [(line 48)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L48)

```
        rates[id].lastAccrued = block.timestamp;
```
>File: contracts/modules/interest-rate/InterestRateCredit.sol [(line 50)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L50)

```
Other instances of this issue are :
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L82
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L58
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132

## `Require` /`revert` should have descriptive reason strings 

```
    require(escrow_.liquidate(amount, targetToken, to));
```
>File: contracts/modules/credit/EscrowedLine.sol [(line 64)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L64)

```
    require(escrow.updateLine(newLine));
```
>File: contracts/modules/credit/EscrowedLine.sol [(line 90)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L90)
```
Other instances of this issue are :
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L112
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L241
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L259
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L326
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L62
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L143
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L147
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L91
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L161
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L198
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L96
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L128
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L180
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L189
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L201
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L130
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L155

## Set `garbage` value in `mapping` for deleting that

If there is a mapping data structure present inside struct, then deleting the struct doesn't delete the mapping. Instead one should use lock to lock that data structure from further use.


```
        delete unusedTokens[token];
```
>File: contracts/modules/credit/SpigotedLine.sol [(line 257)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L257)

```
        delete mutualConsents[expectedHash];
```
>File: contracts/utils/MutualConsent.sol [(line 57)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/MutualConsent.sol#L57)


## Scientific notation
For readability, it is best to use scientific notation (e.g 10e5) rather than decimal literals(100000) or exponentiation(10**5)

```
        uint256 _numerator = collateralValue * 10**5; // scale to 4 decimals
```
>File: contracts/utils/EscrowLib.sol [(line 42)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L42)


## `NatSpec` is incomplete

```
    /// @audit Missing: '@param'
    /**
     * @return price - the latest price in USD to 8 decimals
     */
    function getLatestAnswer(address token) external returns (int) {

```
* File: contracts/modules/oracle/Oracle.sol [(line 19-22)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L19-L22)

```
    /// @audit Missing: '@param' & `return`
    /**
     *   @dev   We don't transfer the ownership of Escrow and Spigot internally
     *          because they aren't owned by the factory, the responsibility falls
     *          on the [owner of the line]
     *   @dev   The `cratio` in the CoreParams are not used, due to the fact
     *          they're passed in when the Escrow is created separately.
     */
    function deploySecuredLineWithModules(

```
* File: contracts/modules/factories/LineFactory.sol [(line 128-135)](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/factories/LineFactory.sol#L128-L135)

```
Other instances of this issue are:
```
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L100-L108	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L118-L125	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L131-L137	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L153-L159	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L165-L170	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L174-L181	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L185-L194	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L202-L206	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L210-L216	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L69-L74		    /// @audit Missing:`param`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L93-L100	    /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L82-L90	    /// @audit Missing:`param`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L212-L218   /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L427-L435   /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L477-L483   /// @audit Missing:`param`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L69-L80      /// @audit Missing:`return`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L186-L194         /// @audit Missing:`param`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotedLineLib.sol#L211-L217         /// @audit Missing:`param`
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L28-L34	                /// @audit Missing:`return`	
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L53-L59	                /// @audit Missing:`return`	
* https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L76-L80	                /// @audit Missing:`return`	
