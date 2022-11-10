1:
CreditLib#create() not check result.length
```
  function create(
      bytes32 id,
      uint256 amount,
      address lender,
      address token,
      address oracle
  )
      external 
      returns(ILineOfCredit.Credit memory credit)
  {
      int price = IOracle(oracle).getLatestAnswer(token);
      if(price <= 0 ) { revert NoTokenPrice(); }

      uint8 decimals;
      if(token == Denominations.ETH) {
          decimals = 18;
      } else {
          (bool passed, bytes memory result) = token.call(
              abi.encodeWithSignature("decimals()")
          );
-         decimals = !passed ? 18 : abi.decode(result, (uint8));
+         decimals = !passed || result.length==0 ? 18 : abi.decode(result, (uint8));          
      }
```


2:
SpigotLib#updateOperator() can only be updated by the Operator, if the Operator is invalid or malicious Operator, the Operator can not be updated again, it is recommended that the owner also has permission to modify the Operator
```
    function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
-       if(msg.sender != self.operator) { revert CallerAccessDenied(); }
+       if(msg.sender != self.operator && msg.sender != self.owner) { revert CallerAccessDenied(); }
        require(newOperator != address(0));
        self.operator = newOperator;
        emit UpdateOperator(newOperator);
        return true;
    }
```

3:
EscrowState#enableCollateral() When decimals() call fails to default 18 bits, there may be a risk if decimals < 18, it is recommended to revert directly

```
    function enableCollateral(EscrowState storage self, address oracle, address token) external returns (bool) {
                (bool successDecimals, bytes memory decimalBytes) = deposit
                    .asset
                    .call(abi.encodeWithSignature("decimals()"));
                if (decimalBytes.length > 0 && successDecimals) {
                    deposit.assetDecimals = abi.decode(decimalBytes, (uint8));
                } else {
-                    deposit.assetDecimals = 18;
+                    revert("bad decimals()")
                }
```

4:
Escrow#constructor() not check minimumCollateralRatio<=10000
```
    constructor(
        uint32 _minimumCollateralRatio,
        address _oracle,
        address _line,
        address _borrower
    ) {
+      require(minimumCollateralRatio<=10000);
        minimumCollateralRatio = _minimumCollateralRatio;
        oracle = _oracle;
        borrower = _borrower;
        state.line = _line;
    }
```

5:
Oracle#getLatestAnswer() using latestRoundData, but there is no check if the return value indicates stale data.

Suggestion:
```
    function getLatestAnswer(address token) external returns (int) {
        (
            uint80 roundID , 
            int price,
            uint80 startedAt,
            uint80 timeStamp,
            uint80 answeredInRound
        ) = registry.latestRoundData(token, Denominations.USD);
        
+       require(answeredInRound >= roundID, "Stale price");
+       require(timeStamp != 0,"Round not complete");
+       require(price > 0,"Chainlink answer reporting 0");

        return price;
    }

```