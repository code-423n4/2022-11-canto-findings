## Pack Struct

Variable can be tightly packed into 1 slot without sacrificing usability

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L15-L18

```solidity
    struct NftData {
        uint248 tokenId;
        bool registered;
    }
```

## NftData.registered is redundant

It seems to be always be true when tokenId is set and therefore can be removed.

