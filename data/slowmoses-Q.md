## Access Control with Modifier

Consider using a modifier for access control.

***

Below are 10 instances of this issue:

        function declareInsolvent() external whileBorrowing returns(bool) {
            if(arbiter != msg.sender) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L143-L144


          if (msg.sender != borrower && msg.sender != credit.lender) {
            revert CallerAccessDenied();
          }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L140-L142


      function liquidate(
        uint256 amount,
        address targetToken
      )
        external
        whileBorrowing
        returns(uint256)
      {
        if(msg.sender != arbiter) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SecuredLine.sol#L80-L88


        function addSpigot(SpigotState storage self, address revenueContract, ISpigot.Setting memory setting) external returns (bool) {
            if(msg.sender != self.owner) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L125-L126


        /** see Spigot.removeSpigot */
        function removeSpigot(SpigotState storage self, address revenueContract)
            external
            returns (bool)
        {
            if(msg.sender != self.owner) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L142-L147


        function updateOwnerSplit(SpigotState storage self, address revenueContract, uint8 ownerSplit)
            external
            returns(bool)
        {
          if(msg.sender != self.owner) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L164-L168


        function updateOwner(SpigotState storage self, address newOwner) external returns (bool) {
            if(msg.sender != self.owner) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L178-L179


        function updateOperator(SpigotState storage self, address newOperator) external returns (bool) {
            if(msg.sender != self.operator) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L187-L188


        function updateTreasury(SpigotState storage self, address newTreasury) external returns (bool) {
            if(msg.sender != self.operator && msg.sender != self.treasury) {
              revert CallerAccessDenied();

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L196-L198


        function updateWhitelistedFunction(SpigotState storage self, bytes4 func, bool allowed) external returns (bool) {
            if(msg.sender != self.owner) { revert CallerAccessDenied(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L208-L209

## Not Implemented Function

Consider moving this function into an abstract contract or an interface.

***


        function _canDeclareInsolvent() internal virtual returns(bool) {
            // logic updated in Spigoted and Escrowed lines
            return true;
        }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L157-L160

## Code Paths Without Return

If there is a return value, then every code-path should explicitly return something.

***


        function _sortIntoQ(bytes32 p) internal returns (bool) {
            uint256 lastSpot = ids.length - 1;
            uint256 nextQSpot = lastSpot;
            bytes32 id;
            for (uint256 i; i <= lastSpot; ++i) {
                id = ids[i];
                if (p != id) {
                    if (
                      id == bytes32(0) ||       // deleted element. In the middle of the q because it was closed.
                      nextQSpot != lastSpot ||  // position already found. skip to find `p` asap
                      credits[id].principal > 0 //`id` should be placed before `p`
                    ) continue;
                    nextQSpot = i;              // index of first undrawn line found
                } else {
                    if(nextQSpot == lastSpot) return true; // nothing to update
                    // swap positions
                    ids[i] = ids[nextQSpot];    // id put into old `p` position
                    ids[nextQSpot] = p;         // p put at target index
                    return true;
                }
    
            }
        }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L516-L538

## Possible Value Corruption

In the assignment in the code below, it is not certain, that block.timestamp really
is the right time for lastAccrued, since accrual does not happen locally.
It should be ok in the current context but this is a potential source of problems.

***


            rates[id] = Rate({
                dRate: dRate,
                fRate: fRate,
                lastAccrued: block.timestamp
            });

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/interest-rate/InterestRateCredit.sol#L79-L83

## Incorrect Comment

There seems to be a "corresponding function". The event is emitted in line 191 in function 'repay'.

***


      event RepayPrincipal(bytes32 indexed id, uint256 indexed amount);
      // Emits that a Borrower has repaid an amount of principal - there is no corresponding function

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L46-L47

## Misleading Function Name

It returns a price. So it should be called be "IOracle(oracle).getLatestPrice(c.token);".

***


        int256 price = IOracle(oracle).getLatestAnswer(c.token);

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L84

## Wrong Variable Type

If 'price' can be negative (type int), then so can be 'value'.
Either 'price' should be 'uint' or 'value' should allow negative numbers as well.

***


        return price <= 0 ? 0 : (amount * uint(price)) / (1 * 10 ** decimals);

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L117

## Underflow

If too much is repayed an underflow might happen.

***


              credit.principal -= principalPayment;

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L186

## Check for Inequality

The "msg.value < amount" code should be "msg.value != amount".
The contract should not collect excess eth. The correct check also saves a drop of gas.

***


                if(msg.value < amount) { revert TransferFailed(); }

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/LineLib.sol#L71

## Language!

If a project is meant to be taken serious, there should be no "fucks up" in comments.

***


            // cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L69

## One Letter Variable Names

Single letter variables names should only be reserved for counter and the like.
The code becomes much more readable when other variables get proper, self-explanatory names.

***

Below are 2 instances of this issue:

                (Credit memory c, uint256 _p, uint256 _i) = CreditLib.getOutstandingDebt(

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L185


        returns (ILineOfCredit.Credit memory c, uint256 principal, uint256 interest)

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L80

## Typo

***

In the following code 'borrower' should read 'borrowed'.

         * @param token - ERC20 token that is being lent and borrower

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/LineOfCredit.sol#L432

***

In the following code 'pat' should read 'pay'.

         * @param targetToken - The credit token that needs to be bought in order to pat down debt. Always `credits[ids[0]].token`

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/SpigotedLine.sol#L182

***

In the following code 'swithc' should read 'switch'.

       * @notice helper function to allow borrower to easily swithc collateral to a new Line after repyment

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L84

***

In the following code 'priviliegad' should read 'privileged'.

       *(@dev priviliegad internal function.

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/modules/credit/EscrowedLine.sol#L85

***

In the following code 'interset' should read 'interest'.

        * @param oracle - interset rate contract used by line that will calculate interest owed

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L123

***

In the following code 'interset' should read 'interest'.

        * @param interest - interset rate contract used by line that will calculate interest owed

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/CreditLib.sol#L237

## Exceptions Used for Control-Flow

Exceptions should not be used to handle normal control flow.
When there is a known potential issue, it should be checked in code and handled with an explicit 'revert'.

***

Below are 2 instances of this issue:

                // claimed = total balance - already accounted for balance
                claimed = existingBalance - self.escrowed[token];
                // underflow revert ensures we have more tokens than we started with and actually claimed revenue

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L37-L39


                // claimed = total balance - existing balance
                claimed = LineLib.getBalance(token) - existingBalance;
                // underflow revert ensures we have more tokens than we started with and actually claimed revenue

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L46-L48

## Sensitive Term 'White'

Consider using 'allowlist' instead of 'whitelist'.

***

Below are 2 instances of this issue:

            // cant claim revenue via operate() because that fucks up accounting logic. Owner shouldn't whitelist it anyway but just in case

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L69


          return self.whitelistedFunctions[func];

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L222

## Open Questions

Finished code should not have any open questions.
Someone should answer them before the project is deployed.

***


            self.escrowed[token] = 0; // keep 1 in escrow for recurring call gas optimizations?

https://github.com/debtdao/Line-of-Credit/blob/audit/code4rena-2022-11-03/contracts/utils/SpigotLib.sol#L117

