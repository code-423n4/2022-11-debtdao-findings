# 1) Use calldata instead of memory
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.
Instance:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L125

Recommended Mitigation Steps:
Consider to replace memory with calldata.


# 2) Reorganize variables according to their value sizes
uint8 and address(20 bytes) both fit into one storage slot (32 bytes).
To save gaz put uint8 at the beginning:
uint8 revenueSplit
address oracle, 

Instance:     
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineFactoryLib.sol#L85

Recommended Mitigation Steps:
Consider rearranging value types so that they could fit in a 32 byte slot


# 3) Mathematical optimizations
Instead of:
unusedTokens[credit.token] -= repaid - newTokens; 

You can rewrite:
unusedTokens[credit.token] = unusedTokens[credit.token]- repaid - newTokens;

2 Instances:
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L122
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L125

Recommended mitigation steps:
Consider rewriting these statements to save gaz and increase readability.
