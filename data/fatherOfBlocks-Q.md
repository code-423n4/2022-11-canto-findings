**CIP-001/src/Turnstile.sol**
- L42/129 - The onlyNftOwner modifier is only used once, therefore it doesn't make sense to create the validation as a modifier, you could save gas directly by using it in the first line inside the withdraw() function.
