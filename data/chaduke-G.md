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
Or we can simply drop the variable ``registered``, if feeRecipient[contract] != 0 then we know it is not registered since there is an NFTid associated with the contract. Let the first NFTid = 1.

G3. https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L134
Check whether ``amount == 0`` separately in the first line to save gas - we can avoid reading the state variable when it is equal to zero.

G4. https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151
Change line 151 to the following to save gas:
```
 balances[_tokenId] = balances[_tokenId] + msg.value;
``` 

G5. https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L137
No need to check overflow, so use unchecked can save gas.
```
unchecked{ balances[_tokenId] = earnedFees - _amount;}
```

G6. https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151
use unchecked to save gas. impossible to overflow here
```
 unchecked{balances[_tokenId]  = balances[_tokenId]+msg.value;
```

