1) uint8,uint32, SIZE CAN BE REPLACED WITH UINT256 . THE SLOT 256 BYTES BLOCK. IF THE SIZE IS LOW IT WILL AUTOMATICALLY CONVERTED INTO UINT256 . SO THERE IS CONVERSION CHARGES. SO CAN DIRECTLY DECLARE TO uint256. SAVE SOME CONVERSION GAS FEE CHARGES.

Same problem in 5 instances.

File : Line-of-Credit/contracts/interfaces/IEscrow.sol

8:     uint8 assetDecimals;

File :  Line-of-Credit/contracts/interfaces/ILineFactory.sol

7:     uint32 cratio;

8:     uint8 revenueSplit;

File :  Line-of-Credit/contracts/interfaces/ILineOfCredit.sol

14:    uint8 decimals;  

File : Line-of-Credit/contracts/interfaces/ISpigot.sol

6:      uint8 ownerSplit;   

--------------------------------------------------------------------------------------------------------------------------------------------

2) WHEN EVER WE ARE USING || OPERATOR WE DON'T NEED TO CHECK THE MULTIPLE CONDITIONS. IF ANY ONE CONDITION true THE OVERALL CONDITION BECCOMES true. IF ONE CONDITION IS true THEN THE REMANING CONDITION CHECK IS NOT NEEDED. IN THIS WAY WE CAN SAVE LOT OF GAS 

Same problem in 4 instances

File :   Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

 84 :    if(count == 0 || credits[ids[0]].principal == 0) 

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L124-L127

https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/LineOfCredit.sol#L523-L527

File :  Line-of-Credit/contracts/modules/credit/SecuredLine.sol

40:     if(SpigotedLine._init() != s || EscrowedLine._init() != s) {

---------------------------------------------------------------------------------------------------------------------------------------------------------------

3) WHEN EVER WE USE && OPERATOR IF ONE CONDTION false THEN THE OVER ALL CONDITION CHECK BECOME false. IN THIS CASE WE DON'T NEED TO CHECK NEXT NEXT CONDITIONS IF ONE CONDITION IS false.

Same problem in 3 instances

File:    Line-of-Credit/contracts/modules/credit/SpigotedLine.sol

102 :   if (msg.sender != borrower && msg.sender != credit.lender) {

 140:   if (msg.sender != borrower && msg.sender != credit.lender) {

File:    Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

132:   if (block.timestamp >= deadline && count > 0) 

------------------------------------------------------------------------------------------------------------------------------------------

4) INSTEAD OF principal += _p; CAN MODIFY TO  principaL=principal+ _p . += ,-= CONSUMES MORE GAS FEE COMPARE TO NORMAL ADDITION AMS SUBTRACTION METHOD.

File:  Line-of-Credit/contracts/modules/credit/LineOfCredit.sol

192:       principal += _p; 
193:       interest += _i;

