QA1: It is advised to use *send* to send eth for the following line: 
 https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/LineLib.sol#L48
Justification: the gas cost for *transfer* might change and might introduce reentrancy attack; see
https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/

