## Use safeMint instead of Mint

Contract:
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L86

Issue:
1. The register function currently make use of _mint function in order to mint NFT to receiver. 

2. Now _mint function does not guarantee that receiver is supporting ERC721 which means post mint NFT can get stuck with receiver

3. This means NFT owner will not be able to transfer this NFT to another user (valid use case as confirmed by product team)

Recommendation:
Make use of _safeMint function instead of _mint function which ensures that receiver is indeed capable to receive the NFT

## Zero address check missing on _recipient

Contract:
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127

Issue:
It was observed that while using `withdraw` function, if NFT owner mistakenly gives _recipient as address(0) then the address is accepted and payment is indeed send to address(0) which means funds will get lost

Recommendation:
Add below check:

```
require(_recipient!=address(0), "Incorrect address");
```