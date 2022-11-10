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