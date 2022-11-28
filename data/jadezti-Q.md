[NC-1] Check on the smart contract address coule be more restrictive

**Instances: 2**

File:   CIP-001/src/Turnstile.sol
[Link to file](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)

Functions `turnstile.register()` and `turnstile.assign()` require the caller must be a `smart contract`. However, there is no such restriction in place. An EOA can all the functions as well.
In order to restrict the caller be a `smart contract`, check the caller's code length.

Recommended changes:
1. Add a custom error to the contract:  `error NotSmartContract();`
2. Add statement `if (smartContract.code.length == 0) revert NotSmartContract();` after [Line 87](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L87) and [Line 108](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L108).
