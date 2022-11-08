# Gas optimization 1 
Code: https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L203-L207
In LineOfCredit.sol  
```

function accrueInterest() external override returns(bool) {
        uint256 len = ids.length;
        bytes32 id;
        for (uint256 i; i < len; ++i) {
          id = ids[i];
          Credit memory credit = credits[id];
          credits[id] = _accrue(credit, id);
        }
        
        return true;
    }

```
There are situations that id = 0 and this function is called,  with id = 0 then the credits[id] does not change. So we can save gas by skipping the following calculation if id = 0. This can be similar to the code in _updateOutstandingDebt() Line 183 
Code: https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L183

Change to

```

function accrueInterest() external override returns(bool) {
        uint256 len = ids.length;
        bytes32 id;
        for (uint256 i; i < len; ++i) {
          id = ids[i];
          // null element in array from closing a position. skip for gas savings
            if(id == bytes32(0)) { continue; }

          Credit memory credit = credits[id];
          credits[id] = _accrue(credit, id);
        }
        
        return true;
    }

```