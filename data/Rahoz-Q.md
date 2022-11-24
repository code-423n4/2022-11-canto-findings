## N. Should check only contract can interact
In function `register` and `assign`, because natspec said that `msg.sender` is assumed to be a smart contract, but a EOA can call to function
We should provide method to check msg.sender should be a contract by checking it code length via function `isContract()` from Openzeppelin-Address library
### Proof of Concept
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L86-L120