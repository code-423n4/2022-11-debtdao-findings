## [L-01] File Is Missing NatSpec

Code should include NatSpec

```
File: ./contracts/modules/oracle/Oracle.sol


```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/oracle/Oracle.sol


## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
File:  ./contracts/modules/interest-rate/InterestRateCredit.sol

48:        uint256 timespan = block.timestamp - rate.lastAccrued;        
50:        rates[id].lastAccrued = block.timestamp;

File: ./contracts/modules/credit/LineOfCredit.sol

132:         if (block.timestamp >= deadline && count > 0) {
      
```
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L132
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L48
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L50


## [L-03] Duplicate if-checks and require statements should be made into modifier

```
File: ./contracts/modules/credit/SpigotedLine.sol

160:         require(msg.sender == borrower);
239:         require(msg.sender == arbiter);
105:         require(msg.sender == ILineOfCredit(self.line).arbiter());
216:         require(msg.sender == self.line);
```

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L160
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L239
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L105
https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/EscrowLib.sol#L216
