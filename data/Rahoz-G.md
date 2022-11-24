## Add unchecked {} for subtractions where the operands cannot underflow because of a previous
In `withdraw`, because we already check that _amount never > earnedFees, we can use uncheck to update balances[_tokenId]
### Proof of Concept
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L135-L137