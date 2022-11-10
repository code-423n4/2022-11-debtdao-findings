### [L-01] ```approve()``` and ```safeApprove()``` should be replaced with ```safeIncreaseAllowance()``` / ```safeDecreaseAllowance()```


#### Impact
```approve()``` & ```safeApprove()``` are deprecated and subject to a known front-running attack. Consider using  ```safeIncreaseAllowance()``` & ```safeDecreaseAllowance()``` instead.


#### Findings:
```
contracts/utils/SpigotedLineLib.sol:L134                     IERC20(sellToken).approve(swapTarget, amount);

```

### [L-02] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/mock/ZeroEx.sol:L1     pragma solidity ^0.8.9;

contracts/modules/credit/SpigotedLine.sol:L1     pragma solidity ^0.8.9;

contracts/modules/credit/SecuredLine.sol:L1     pragma solidity ^0.8.9;

contracts/modules/credit/LineOfCredit.sol:L1     pragma solidity ^0.8.9;

contracts/modules/oracle/Oracle.sol:L2     pragma solidity ^0.8.9;

contracts/modules/interest-rate/InterestRateCredit.sol:L1     pragma solidity ^0.8.9;

```
### [L-03] ```require()```/```revert()``` statements should have descriptive strings.


#### Impact
Consider adding descriptive strings in ```require()```/```revert()```.


#### Findings:
```
contracts/utils/SpigotedLineLib.sol:L147      require(ISpigot(spigot).updateOwner(newLine));

contracts/utils/SpigotLib.sol:L96            require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));

contracts/utils/SpigotLib.sol:L128        require(revenueContract != address(this));

contracts/utils/SpigotLib.sol:L130        require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));

contracts/utils/SpigotLib.sol:L155        require(success);

contracts/utils/SpigotLib.sol:L180        require(newOwner != address(0));

contracts/utils/SpigotLib.sol:L189        require(newOperator != address(0));

contracts/utils/SpigotLib.sol:L201        require(newTreasury != address(0));

contracts/utils/EscrowLib.sol:L91        require(amount > 0);

contracts/utils/EscrowLib.sol:L105        require(msg.sender == ILineOfCredit(self.line).arbiter());

contracts/utils/EscrowLib.sol:L161        require(amount > 0);

contracts/utils/EscrowLib.sol:L198        require(amount > 0);

contracts/utils/EscrowLib.sol:L216      require(msg.sender == self.line);

contracts/mock/MockLine.sol:L39        require(msg.sender == arbiter);

contracts/modules/credit/SpigotedLine.sol:L62        require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

contracts/modules/credit/SpigotedLine.sol:L143      require(amount <= unusedTokens[credit.token]);

contracts/modules/credit/SpigotedLine.sol:L160        require(msg.sender == borrower);

contracts/modules/credit/SpigotedLine.sol:L239        require(msg.sender == arbiter);

contracts/modules/credit/LineOfCredit.sol:L112        require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

contracts/modules/credit/LineOfCredit.sol:L241        require(interestRate.setRate(id, drate, frate));

contracts/modules/credit/LineOfCredit.sol:L259        require(interestRate.setRate(id, drate, frate));

contracts/modules/credit/LineOfCredit.sol:L326        require(amount <= credit.principal + credit.interestAccrued);

contracts/modules/credit/EscrowedLine.sol:L64    require(escrow_.liquidate(amount, targetToken, to));

contracts/modules/credit/EscrowedLine.sol:L90    require(escrow.updateLine(newLine));
```

### [L-04] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
contracts/mock/RevenueToken.sol:L8        _mint(account, amount);

```
### [L-05] zero-address checks are missing


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/MockEscrowedLine.sol#L20-L21
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/MockLine.sol#L16
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/MockLine.sol#L21
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/MockLine.sol#L26
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/RevenueToken4626.sol#L11
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/RevenueToken4626.sol#L16
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/SimpleOracle.sol#L12-L13
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/SimpleRevenueContract.sol#L10-L11
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/mock/SimpleRevenueContract.sol#L40
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L17
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L55-L57
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L23-L27
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L30
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L61
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L64
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L66
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/escrow/Escrow.sol#L49-L51
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol#L16
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L36-L38


### [N-01] Open TODOs


#### Impact
Code architecture, incentives, and error handling/reporting questions/issues should be resolved before deployment.


#### Findings:
```
contracts/modules/factories/LineFactory.sol:L140        // TODO: test

contracts/modules/factories/LineFactory.sol:L145        // TODO: test

```
