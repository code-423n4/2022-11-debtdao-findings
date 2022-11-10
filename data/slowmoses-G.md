## Useless Redirect

A function should do more than just pass all arguments on to some other function.
Removing a useless redirect from the project saves gas in deployment and in execution.

***

Below are 3 instances of this issue:

        /// see ILineOfCredit.updateOutstandingDebt
        function updateOutstandingDebt() external override returns (uint256, uint256) {
            return _updateOutstandingDebt();
        }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L162-L165


        function _accrue(Credit memory credit, bytes32 id) internal returns(Credit memory) {
          return CreditLib.accrue(credit, id, address(interestRate));
        }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L218-L220


        function accrueInterest(
            bytes32 id,
            uint256 drawnBalance,
            uint256 facilityBalance
        ) external override onlyLineContract returns (uint256) {
            return _accrueInterest(id, drawnBalance, facilityBalance);
        }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L34-L40

## Fixed Return Value Function

When a return value of a function can only ever take one fixed value, for example 'false' or '42',
then this return value should be removed from the function signature to save deployment gas and execution gas.

Note that reverts and the like do not change anything about a fixed return value.

***

Below are 14 instances of this issue:

        function borrow(bytes32 id, uint256 amount)
            external
            override
            whileActive
            onlyBorrower
            returns (bool)
        {
            Credit memory credit = _accrue(credits[id], id);
    
            if(amount > credit.deposit - credit.principal) { revert NoLiquidity(); }
    
            credit.principal += amount;
    
            credits[id] = credit; // save new debt before healthcheck
    
            // ensure that borrowing doesnt cause Line to be LIQUIDATABLE
            if(_updateStatus(_healthcheck()) != LineLib.STATUS.ACTIVE) {
                revert NotActive();
            }
    
            LineLib.sendOutTokenOrETH(credit.token, borrower, amount);
    
            emit Borrow(id, amount);
    
            _sortIntoQ(id);
    
            return true;
        }
    

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L340-L368


        function _close(Credit memory credit, bytes32 id) internal virtual returns (bool) {
            if(credit.principal > 0) { revert CloseFailedWithPrincipal(); }
    

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L483-L485


        function useAndRepay(uint256 amount) external whileBorrowing returns(bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L137


      function rollover(address newLine)

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L48


      function _canDeclareInsolvent() internal virtual returns(bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L77


      function _rollover(address newLine) internal virtual returns(bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L89


        function operate(SpigotState storage self, address revenueContract, bytes calldata data) external returns (bool) {
            if(msg.sender != self.operator) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L61-L62


        function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125


        /** see Spigot.removeSpigot */
        function removeSpigot(SpigotState storage self, address revenueContract)

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L142-L143


        function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L164


        function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178


        function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L187


        function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L196


        function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L208

## Do-Nothing Contract

The whole 'Spigot' contract does nothing but pass data through to SpigotState.
This strikes me as quite a waste of gas.
Consider changing the architecture of the program to avoid this.

***


    contract Spigot is ISpigot, ReentrancyGuard {
        using SpigotLib for SpigotState;
    
        // Stakeholder variables
    

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/spigot/Spigot.sol#L16-L20

