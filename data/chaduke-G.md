G1. https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L49-L55
There is no need to introduce the local variable ``smartContract``
``` 
 modifier onlyUnregistered() {
        if (isRegistered(msg.sender)) revert AlreadyRegistered();

        _;
    }
```

G2. https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L15-L18
Defining the varaible ``registered`` as uint256 can save gas for two reasons: 1) no need to convert bool to a 32 slot, 2) change from 0 to 1 is more expensive then change from non-zero to non-zero. WE can encode true as 2 and false as 1.
```
 struct NftData {
        uint256 tokenId;
        uint registered;
    }

```
