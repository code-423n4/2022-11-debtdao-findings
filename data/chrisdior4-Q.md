# [N-01] Code Style: constants should be named in all caps

Here are some examples that the code style does not follow the best practices:

1.uint8 constant defaultRevenueSplit = 90;

- https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L12

2.uint32 constant defaultMinCRatio = 3000;

- https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/factories/LineFactory.sol#L14

=================================

# [N-02] The nonReentrant modifier should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

1.function claimAndRepay(address claimToken, bytes calldata zeroExTradeData)
external
whileBorrowing
`nonReentrant`

- https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L93

2.function claimAndTrade(address claimToken, bytes calldata zeroExTradeData)
external
whileBorrowing
`nonReentrant`

- https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/modules/credit/SpigotedLine.sol#L154

=================================

# [N-03] USE SCIENTIFIC NOTATION (E.G. 1E18) RATHER THAN EXPONENTIATION (E.G. 10**18)

Scientific notation should be used for better code readability

1.uint256 _numerator = collateralValue * 10**5;

- https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L42