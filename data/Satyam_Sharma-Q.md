In contract of function LineOfCredit.counts always returing default values: 

function counts() external view returns (uint256, uint256) {
        return (count, ids.length);
    }
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L117

No use of function counts in contract LineOfCredit only a wastage of gas as it uses and return only the default values.
