[GAS-1] Use `unchecked{}` where overflow/underflow won't happen

```
File:   CIP-001/src/Turnstile.sol

// @audit: Line 135 guarantees _amount <= earnedFees
137:    balances[_tokenId] = earnedFees - _amount;

```

[Link to file](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)
