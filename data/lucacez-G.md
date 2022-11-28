### X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

File: Turnstile.sol [line 151](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151)
```
        balances[_tokenId] += msg.value;
```
### USING BOOLS FOR STORAGE INCURS OVERHEAD
File: Turnstile.sol [line 17](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L17)
```
        bool registered;
```