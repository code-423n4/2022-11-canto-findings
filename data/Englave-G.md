#### Functions that can be declared external
“public” functions never called by the contract should be declared “external” to save gas.
Solidity immediately copies array arguments to memory in public functions, while external functions can read directly from the calldata. Memory allocation is expensive, whereas reading from calldata is cheap.

**Path:** ./src/Turnstile.sol : register(), distributeFees(), assign(), withdraw()
**Recommendation:** Use external instead of public