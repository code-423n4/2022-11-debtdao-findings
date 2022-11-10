Unlocked pragma
Contracts should be deployed using the same compiler version/flags with which they have been tested. Locking the pragma (for e.g. by not using ^ in pragma solidity 0.5.10) ensures that contracts do not accidentally get deployed using an older compiler version with unfixed bugs.
https://swcregistry.io/docs/SWC-103
ex:
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L1
==========================================================

Unhandled return values of transfer and transferFrom: ERC20 implementations are not always consistent. Some implementations of transfer and transferFrom could return ‘false’ on failure instead of reverting. It is safer to wrap such calls into require() statements to these failures.
Recommendation: Check the return value and revert on 0/false or use OpenZeppelin’s SafeERC20 wrapper functions
Medium severity finding from Consensys Diligence Audit of Aave Protocol V2: https://consensys.net/diligence/audits/2020/09/aave-protocol-v2/#unhandled-return-values-of-transfer-and-transferfrom

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48
==========================================================

Reentrancy vulnerabilities: Untrusted external contract calls could callback leading to unexpected results such as multiple withdrawals or out-of-order events. Use check-effects-interactions pattern or reentrancy guards. (see here: https://swcregistry.io/docs/SWC-107)
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L59
==========================================================

ERC20 approve() race condition: Use safeIncreaseAllowance() and safeDecreaseAllowance() from OpenZeppelin’s SafeERC20 implementation to prevent race conditions from manipulating the allowance amounts. (see here: https://swcregistry.io/docs/SWC-114)

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L134
==========================================================

fallback vs receive(): Check that all precautions and subtleties of fallback/receive functions related to visibility, state mutability and Ether transfers have been considered.  (see here: https://docs.soliditylang.org/en/latest/contracts.html#fallback-function and here: https://docs.soliditylang.org/en/latest/contracts.html#receive-ether-function )

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L272
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/spigot/Spigot.sol#L227
==========================================================

Dangerous strict equalities: Use of strict equalities with tokens/Ether can accidentally/maliciously cause unexpected behavior. Consider using >= or <= instead of == for such variables depending on the contract logic. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#dangerous-strict-equalities )

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L106
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L163
==========================================================

Account existence check for low-level calls: Low-level calls call/delegatecall/staticcall return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#low-level-calls )

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L142
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L65
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotedLineLib.sol#L131
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L43
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L76
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L149
==========================================================

Unindexed event parameters: Parameters of certain events are expected to be indexed (e.g. ERC20 Transfer/Approval events) so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events. (see here:  https://github.com/crytic/slither/wiki/Detector-Documentation#unindexed-erc20-event-oarameters )
Events Indexing
Events should use the maximum amount of indexed fields: up to three parameters. This makes it easier to filter for specific values in front-ends.

Proof of Concept

Recommended Mitigation Steps Add indexed fields to these events so that they have the maximum number of indexed fields possible.

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/IEscrowedLine.sol#L4
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L13-L15
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/interfaces/ISpigot.sol#L21-L23
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L241
==========================================================
