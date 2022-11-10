1)X = X + Y IS CHEAPER THAN X += Y  


File: Line-of-Credit\contracts\modules\credit\LineOfCredit.sol
  192,23:             principal += _p;
  193,22:             interest += _i;
  276,24:         credit.deposit += amount;
  351,26:         credit.principal += amount;

File: Line-of-Credit\contracts\modules\credit\LineOfCredit.sol:
  191              // update total outstanding debt
  192:             principal += _p;
  193:             interest += _i;
  194              // save changes to storage

  275  
  276:         credit.deposit += amount;
  277          

  350  
  351:         credit.principal += amount;
  352  

File: Line-of-Credit\contracts\modules\credit\SpigotedLine.sol:
  124              // high revenue and bought more than we need
  125:             unusedTokens[credit.token] += newTokens - repaid;
  126          }

  171          // add bought tokens to unused balance
  172:         unusedTokens[targetToken] += newTokens;
  173          return newTokens;



File: Line-of-Credit\contracts\utils\CreditLib.sol:
  177            credit.interestAccrued -= amount;
  178:           credit.interestRepaid += amount;
  179            emit RepayInterest(id, amount);

  186            credit.principal -= principalPayment;
  187:           credit.interestRepaid += interest;
  188            credit.interestAccrued = 0;

  257        // update credit line balance
  258:       credit.interestAccrued += accruedToken;
  259  

File: Line-of-Credit\contracts\utils\EscrowLib.sol:
  74  
  75:                 collateralValue += CreditLib.calculateValue(
  76                    o.getLatestAnswer(d.asset),

  95  
  96:         self.deposited[token].amount += amount;
  97  
  
  
2)Use assembly to check for address(0)
Impact

File: Line-of-Credit\contracts\modules\credit\LineOfCredit.sol
  445,34:         if(credits[id].lender != address(0)) { revert PositionExists(); }


File: Line-of-Credit\contracts\modules\factories\LineFactory.sol
  27,25:         if (arbiter_ == address(0)) {
  30,24:         if (oracle_ == address(0)) {
  33,28:         if (swapTarget_ == address(0)) {
  141,24:         if (mSpigot == address(0)) {
  146,24:         if (mEscrow == address(0)) {
  183,24:         if (arbiter == address(0)) {

File: Line-of-Credit\contracts\utils\LineLib.sol
  42,21:         if(token == address(0)) { revert TransferFailed(); }
  67,21:         if(token == address(0)) { revert TransferFailed(); }
  81,21:         if(token == address(0)) return 0;

File: Line-of-Credit\contracts\utils\SpigotLib.sol
  180,29:         require(newOwner != address(0));
  189,32:         require(newOperator != address(0));
  201,32:         require(newTreasury != address(0));  
