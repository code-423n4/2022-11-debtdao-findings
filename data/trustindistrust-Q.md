## Low

### Funds transfer inside claim function lacks `require` check for its success, unlike its counterpart

Locations:
[1](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L96)
[2](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/SpigotLib.sol#L115)

#### Description

`SpigotLib.claimRevenue()` encapsulates a transfer function from another library (`LineLib.sendOutTokenOrEth`) inside a `require,` as it typically prudent for such effects.

However, `SpigotLib.claimEscrow()` lacks the same check in a semantically similar action, claiming escrowed tokens. This transfer should also be protected by a `require`

#### Suggested course of action

Add a `require` check to line 115.

## Low

### Privileged function lacks 0 and self-reference safety checks

[Location](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/EscrowLib.sol#L215)

#### Description

`EscrowLib.updateLine()` lacks checks to ensure that a 0 address isn't passed as a value, or  setting itself (`address(this)`). This is important because the documentation in the `Escrow` contract states:

> Line has no way to interface with this function so once transfered `line` is set forever

#### Suggested course of action

Add checks to ensure that deployment/management mistakes don't cause permanent loss of ownership of the Escrow contract.

## Low

### Call to determine precision of a token returns hardcoded value instead of a revert in certain circumstances

[Location](https://github.com/debtdao/Line-of-Credit/blob/e8aa08b44f6132a5ed901f8daa231700c5afeb3a/contracts/utils/CreditLib.sol#L145)

#### Description

The function `CreditLib.create()` performs a check against a ERC20 token in order to retrieve the number of decimals the token uses. 

The check as implemented will return a static value of 18 if the `token.call()` returns false. While it is deeply unlikely that a token accepted as credit would ever fall into a state where this call would fail, treating it like nothing has occurred seems unsafe. Additionally, if the token didn't use the typical 18 decimals, defaulting to 18 here could have unpredictable results.

#### Suggested course of action

Since this function is embedded in a larger atomic transaction, it would be better to revert on `false` rather than to forge forward and risk unpredictable state. Since the call is likely to never fail, the revert is likely to never fire, causing minimal friction when credit is being adjusted.

## Non-critical

### Style: Replace instances of maximum integer literal with `type(uint256).max`

Locations:
[1](https://github.com/debtdao/Line-of-Credit/blob/0e6388bc5d255777760f9eafec0b723bdc924fef/deploy-testnet/DeployScript.s.sol#L25)
[2](https://github.com/debtdao/Line-of-Credit/blob/f32cb3eeb08663f2456bf6e2fba21e964da3e8ae/contracts/utils/EscrowLib.sol#L26)

#### Description

The codebase contains instances of `115792089237316195423570985008687907853269984665640564039457584007913129639935`, the maximum `uint256` value. This can be replaced with a shorthand `type(uint256).max`, resulting in better code clarity.