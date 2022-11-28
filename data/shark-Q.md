## Revert as early as possible
Conditional checks should appear as early as possible in a function. If a revert were to occur, it would avoid incurring extra gas on code coming after the checks.

The following line of code could be placed before Line 87:
File: `Turnstile.sol`: [Line 89](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L89)