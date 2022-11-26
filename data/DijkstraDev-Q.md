Withdraw:
- Lacks a non-zero validation. Rewards may be lost if user does not provide a valid recipient address [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L141).

DistributeFees: 
- Lacks a validation of the given Id token. Funds could be allocated to an NFT that hasn't been minted yet, giving free rewards to a future developer [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151).