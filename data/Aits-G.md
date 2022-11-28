 ###  a += b costs more gas than a = a + b for state variables

Using the addition operator instead of plus-equals saves Gas

There are 1 instances of this issue:

```
File:  CIP-001/src/Turnstile.sol

L151:       balances[_tokenId] += msg.value;


```
