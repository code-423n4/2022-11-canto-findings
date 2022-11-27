# Canto Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-00      | Use 2-Step-Process for transferring ownership | Turnstile.sol | 1 |
| L-01      | Missing zero address check | Turnstile.sol | 1 |
| L-02      | Missing check that `tokenId` exists | Turnstile.sol | 1 |


## [L-00] Use 2-Step-Process for transferring ownership
The `Turnstile` contract inherits from the OpenZeppelin `Ownable` contract.  

The `Ownable` contract exposes the `transferOwnership` function which allows transferring ownership in a single step.  

It is best practice to use a 2-Step-Process for transferring ownership. This prevents transferring ownership to an address that one has no access to which results in a loss of ownership.  

This 2-Step-Process can be implemented by inheriting from OpenZeppelin's `Ownable2Step` ([https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)) contract instead of the `Ownable` contract.  

## [L-01] Missing zero address check
The `Turnstile.withdraw` function ([https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L127](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L127)) allows to withdraw earned fees to a `_recipient` address.  

However there is no zero address check for the `_recipient` parameter which can lead to a loss of the earned fees.  

## [L-02] Missing check that `tokenId` exists
The `Turnstile.distributeFees` function ([https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148)) is used to send ETH to the `Turnstile` contract which can then be withdrawn by the owner of a specific NFT.  

However there is no check that the NFT with the `_tokenId` parameter actually exists.  

This can cause ETH to be deposited into the `Turnstile` contract that cannot be withdrawn again because they are not associated with an existing `_tokenId`.  

Fix:  
Add `if (!_exists(_tokenId)) revert InvalidTokenId();` to the function.  
