# [G-01] x += y COSTS MORE GAS THAN x= x + y FOR STATE VARIABLES

Using the addition operator instead of plus-equals saves 113 gas

 balances[_tokenId] += msg.value;

- https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151