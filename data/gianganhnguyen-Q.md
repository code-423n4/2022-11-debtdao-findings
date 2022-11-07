# 1. [N-01] Commented code

Please delete code snippet below as it serves no purpose.

    File contracts/modules/oracle/Oracle.sol, line 24:        /* uint80 roundID */, 
    File contracts/modules/oracle/Oracle.sol, line 26:        /* uint80 startedAt */,
    File contracts/modules/oracle/Oracle.sol, line 27:        /* uint80 timeStamp */,
    File contracts/modules/oracle/Oracle.sol, line 28:        /* uint80 answeredInRound */

# 2. [L-01] Uncheck `transfer` in *contracts/BlurExchange.sol*

Boolean return value for `transfer` is not checked in file `contracts/utils/LineLib.sol` line 48:

    payable(receiver).transfer(amount); // I recommend:  require(payable(to).transfer(amount), "!transfer");