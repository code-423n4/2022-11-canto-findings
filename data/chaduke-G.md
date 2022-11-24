G1. https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L49-L55
There is no need to introduce the local variable ``smartContract``
``` 
 modifier onlyUnregistered() {
        if (isRegistered(msg.sender)) revert AlreadyRegistered();

        _;
    }
```