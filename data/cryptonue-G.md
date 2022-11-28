# [G] Flatten modifier which only being used once


The `onlyNftOwner` modifier is only being used once, thus it's better to flatten it to the function calling it, which is the `withdraw` function, to save some gas.

```solidity
File: Turnstile.sol
42:     modifier onlyNftOwner(uint256 _tokenId) {
43:         if (ownerOf(_tokenId) != msg.sender) revert NotAnOwner();
44: 
45:         _;
46:     }
```