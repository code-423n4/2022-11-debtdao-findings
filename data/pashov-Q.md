********************L-01:******************** The code is missing non-zero address checks for constructor arguments. Add such checks to every constructor

******NC-01:****** The code uses a floating pragma in places - use an exact solidity compiler version everywhere to get the same bytecode.

**************NC-02:************** A big part of the codebase is missing NatSpec docs on external methods - as a best practice add the missing docs

******NC-03:****** There are lots of `require` statements that have no revert error string message. Add such a message so error debugging is easier.

******NC-04:****** Remove all comments that are in “TODO” state ( for example  `// keep 1 in escrow for recurring call gas optimizations?`) - either implement suggestion or remove comment

****************NC-05:**************** Since `LineLib` is not complying to any standard there is no need to return a boolean when you always either return true or revert. Remove the boolean result

****NC-06:**** `deploySecuredLine` method does not emit `DeployedSecuredLine` event - add it

**************NC-07:************** Use latest Solidity version to get the latest bugfixes and compiler features/optimizations

**************NC-08:************** Remove unused error `BadToken()` - it is not referenced anywhere

**************NC-09:************** Add a license identifier comment to every solidity file in the codebase, like `// SPDX-License-Identifier: MIT`