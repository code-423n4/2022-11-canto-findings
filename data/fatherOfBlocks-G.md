**CIP-001/src/Turnstile.sol**
- L69 - Instead of using the isRegistered() function to query a value from storage, it is less expensive to use the value directly: feeRecipient[_smartContract].registered.

- L89 - Use assembly to check for address(0), Saves 6 gas per instance.

- L137 - The operation earnedFees - _amount can be unchecked since line 135 validates that _amount > earnedFees, therefore it is impossible for an underflow to be generated.
