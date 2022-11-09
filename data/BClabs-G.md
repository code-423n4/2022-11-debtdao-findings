Gas:
Arbiter cant be address(0) since it's checked in the constructor and is immutable.
https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L183-L185

gas:
defaultMinCRatio can never be larger than 10.000, so uint16 would be sufficient.
