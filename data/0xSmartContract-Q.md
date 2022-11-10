### Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:----:|:-------|:--:|
|[L-01]|Require messages are too short or not | 23 |

Total 1 issue

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01 ]|Using Vulnerable Version of Openzeppelin| |
| [NC-02] |`0 address` check |7|
| [NC-03] | `Function writing` that does not comply with the `Solidity Style Guide` | All contracts |
| [NC-04] |  Omissions in Events | 4 |
| [NC-05] | Use a more recent version of Solidity|  |
| [NC-06] | Solidity compiler optimizations can be problematic |  |
| [NC-07] | Open TODOS | 2 |
| [NC-08] |NatSpec is missing  | 3 
| [NC-09] |Compliance with Solidity Style rules in Constant expressions  | 2 |
| [NC-10] |Need Fuzzing test | 6 |
| [NC-11] |Add NatSpec Mapping comment | 10 |

Total 11 issues


### Suggestions
| Number | Suggestion Details |Context|
|:----:|:-------|:--:|
| [S-01] |Generate perfect code headers every time  | 2 |

Total 1 suggestion



### [L-01] Require messages are too short or not

**Context:**



**Description:**
The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly. Error definitions should be added to the require block, not exceeding 32 bytes.


```solidity

23 results - 6 files

contracts/modules/credit/EscrowedLine.sol:

  64:          require(escrow_.liquidate(amount, targetToken, to));

  90:          require(escrow.updateLine(newLine));


contracts/modules/credit/LineOfCredit.sol:
  112:         require(uint(status) >= uint( LineLib.STATUS.ACTIVE));

  241:         require(interestRate.setRate(id, drate, frate));

  259:         require(interestRate.setRate(id, drate, frate));

  326:         require(amount <= credit.principal + credit.interestAccrued);

contracts/modules/credit/SpigotedLine.sol:
   62:         require(defaultRevenueSplit_ <= SpigotedLineLib.MAX_SPLIT);

  143:         require(amount <= unusedTokens[credit.token]);

  160:         require(msg.sender == borrower);

  239:         require(msg.sender == arbiter);

contracts/utils/EscrowLib.sol:
   91:         require(amount > 0);

  105:         require(msg.sender == ILineOfCredit(self.line).arbiter());

  161:         require(amount > 0);

  198:         require(amount > 0);

  216:         require(msg.sender == self.line);

contracts/utils/SpigotedLineLib.sol:
  147:         require(ISpigot(spigot).updateOwner(newLine));

contracts/utils/SpigotLib.sol:
   96:         require(LineLib.sendOutTokenOrETH(token, self.treasury, claimed - escrowedAmount));
    
  128:         require(revenueContract != address(this));

  130:         require(self.settings[revenueContract].transferOwnerFunction == bytes4(0));

  155:         require(success);

  180:         require(newOwner != address(0));

  189:         require(newOperator != address(0));

  201:         require(newTreasury != address(0));


```
### [N-01]  Using Vulnerable Version of Openzeppelin

The package.json configuration file says that the project is using 4.7.0 of OpenZeppelin 

openzeppelin/contracts vulnerabilities:
https://security.snyk.io/package/npm/@openzeppelin%2Fcontracts/


```js

lib/openzeppelin-contracts/package.json:
  1: {
  2:   "name": "openzeppelin-solidity",
  3:   "description": "Secure Smart Contract library for Solidity",
  4:   "version": "4.7.0",
  5:   "files": [

```

**Recommendation:**
Use   OZ last 4.8.0 versions

### [N-02] `0 address` check

**Context:**

```solidity
7 results - 7 files

contracts/modules/credit/EscrowedLine.sol:
  15  
  16:   constructor(address _escrow) {
  17      escrow = IEscrow(_escrow);

contracts/modules/credit/LineOfCredit.sol:

  49:     constructor(
  50          address oracle_,

contracts/modules/credit/SecuredLine.sol:
  12  
  13:     constructor(
  14          address oracle_,

contracts/modules/credit/SpigotedLine.sol:
  52       */
  53:     constructor(
  54          address oracle_,

contracts/modules/factories/LineFactory.sol:
  19  
  20:     constructor(
  21          address moduleFactory,


contracts/modules/oracle/Oracle.sol:
  15:     constructor(address _registry) {
  16          registry = FeedRegistryInterface(_registry);

contracts/modules/spigot/Spigot.sol:
  30       */
  31:     constructor (
  32          address _owner,



```

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (oracle == address(0)) revert ADDRESS_ZERO();`


### [N-03] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-04] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;

```solidity

contracts/modules/credit/LineOfCredit.sol:

  260:       emit SetRates(id, drate, frate);

  282:       emit IncreaseCredit(id, amount);

  362:       emit Borrow(id, amount);
 
  423:       emit UpdateStatus(uint256(status_));

```
### [N-05] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(^0.8.9)`, newer version can be used `(0.8.17)` 

### [N-06] Solidity compiler optimizations can be problematic


```js
foundry.toml:
  29  offline = false
  30: optimizer = true
  31: optimizer_runs = 200

```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.



**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

### [N-07]  Open TODOS


**Context:**
```solidity

2 results - 1 files

contracts/modules/factories/LineFactory.sol:
  139      ) external returns (address line) {
  140:         // TODO: test
  141          if (mSpigot == address(0)) {

  144  
  145:         // TODO: test
  146          if (mEscrow == address(0)) {

```

**Recommendation:**
Use temporary TODOs as you work on a feature, but make sure to treat them before merging. Either add a link to a proper issue in your TODO, or remove it from the code.

### [N-08] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

**Context:**
[CreditLib.sol#L16](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L16)
[EscrowLib.sol#L12](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L12)
[EscrowLib.sol#L192](https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L192)

### [N-09] Compliance with Solidity Style rules in Constant expressions

**Context:**

```solidity
contracts/modules/factories/LineFactory.sol:
  12:     uint8 constant defaultRevenueSplit = 90; // 90% to debt repayment
  14:     uint32 constant defaultMinCRatio = 3000; // 30.00% minimum collateral ratio 

```

**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.

### [N-10] Need Fuzzing test

**Context:**

```js

6 results - 3 files

contracts/modules/credit/LineOfCredit.sol:
  451:         unchecked { ++count; }

  499:         unchecked { --count; }
 

contracts/utils/CreditLib.sol:
  174      returns (ILineOfCredit.Credit memory)
  175:   { unchecked {
  176        if (amount <= credit.interestAccrued) {

  208      returns (ILineOfCredit.Credit memory)
  209:   { unchecked {
  210        if(amount > credit.deposit - credit.principal + credit.interestRepaid) {

  245      returns (ILineOfCredit.Credit memory)
  246:   { unchecked {
  247        // interest will almost always be less than deposit

contracts/utils/SpigotedLineLib.sol:
  110            );
  111:         } else { unchecked {


```

**Description:**
In total 3 contracts, 6 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [N-11]  Add NatSpec Mapping comment

**Description:**
Add NatSpec comments describing mapping keys and values


**Context:**

```solidity

10 results - 6 files

contracts/modules/credit/LineOfCredit.sol:
  34  
  35:     mapping(bytes32 => Credit) public credits;

contracts/modules/credit/SpigotedLine.sol:
  
  40:     mapping(address => uint256) private unusedTokens;
  

contracts/modules/interest-rate/InterestRateCredit.sol:
  13      address immutable lineContract;
  14:     mapping(bytes32 => Rate) public rates; // position id -> lending rates
  

contracts/utils/EscrowLib.sol:
  16:     mapping(address => bool) enabled;
  18:     mapping(address => IEscrow.Deposit) deposited;

contracts/utils/MutualConsent.sol:
  
  14:     // Mapping of upgradable units and if consent has been initialized by other party
  15:     mapping(bytes32 => bool) public mutualConsents;
  

contracts/utils/SpigotLib.sol:
  12:     mapping(address => uint256) escrowed; // token  -> amount escrowed
  14:     mapping(bytes4 => bool) whitelistedFunctions; // function -> allowed
  16:     mapping(address => ISpigot.Setting) settings; // revenue contract -> settings



```
**Recommendation:**

```solidity

 /// @dev    address(user) -> address(ERC0 Contract Address) -> uint256(allowance amount from user)
    mapping(address => mapping(address => uint256)) public allowance;

```


### [S-01] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```