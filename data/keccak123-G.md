# G-01

[This math](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L138) can be unchecked because of [the operation 2 lines earlier](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L136).

```diff
- balances[_tokenId] = earnedFees - _amount;
+ unchecked { balances[_tokenId] = earnedFees - _amount; }
```

# G-02

Public functions can be changed to external functions to save gas. Public functions that can be changed to external are:
1. register
2. assign
3. withdraw
4. distributeFees