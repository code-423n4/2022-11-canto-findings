# QA Report

1. [Turnstile.register()](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L92) should probably use `_safeMint()` instead of `_mint()` to ensure the minted NFT doesn't end up in a contract that doesn't support it. Especially when there is no way for contracts to migrate to a different `tokenId` if the NFT becomes stuck.