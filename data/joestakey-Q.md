 ## Summary
 ### Low
|      | Issue                                                                    |
|------|--------------------------------------------------------------------------|
| L-01 | Add extra checks during minting to prevent frozen `CANTO` in `Turnstile` |
| L-02 | csr store logic cannot handle `nftIds` larger than `type(uint64).max`    |
 
 ### Non-critical
|      | Issue                                                                               |
|------|-------------------------------------------------------------------------------------|
| N-01 | Typos                                                                               |
| N-02 | Redundant checks                                                                    |
| N-03 | `public` functions not called by the contract should be declared `external` instead |
 
 ## Low 
 ### [L‑01] Add extra checks during minting to prevent frozen `CANTO` in `Turnstile`
 Upon registering a smart contract in `Turnstile.register`, an ownership NFT is minted to the `_recipient` specified as a function argument by the smart contract.
 
 The minting is performed using `_mint()`. As per OpenZeppelin recommendations, this function should be avoided as it lacks safeguards and does not check whether the recipient can handle `ERC721` tokens if it is a smart contract.
 
 The issue here is if the `_recipient` does not handle `ERC721` tokens, as fees will be transferred to the `Turnstile` smart contract in [PostTxProcessing](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/x/csr/keeper/evm_hooks.go#L86), the `balances[_tokenId]` will be incremented, but that amount will never be able to be retrieved, essentially freezing that `CANTO` in the contract.
 
 ```solidity
92:         _mint(_recipient, tokenId);
 ```
 
 Consider using `_safeMint()` instead. This does allow reentrancy, so appropriate reentrancy protection should be added.
 
 ### [L‑02] csr store logic cannot handle `nftIds` larger than `type(uint64).max`

 The distribution logic on the client side retrieves the `nftId` associated with a smart contract via `GetNFTByContract`. This function [computes the id](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/x/csr/keeper/csr.go#L36) returned into a `uint64`.

 But in `Turnstile`, `tokenId` is a `uint256`, and [can exceed](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/8c9a831596788f266ec6e5bf8ef2c7d05bb5931b/contracts/utils/Counters.sol#L28) `type(uint64).max`.

 If an ownership NFT was minted with `tokenId > type(uint64).max`, the smart contract associated would never accrue fees as `PostTxProcessing` would always revert [here](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/x/csr/keeper/csr.go#L36) upon retrieving the nft id.

 As `type(uint64).max` is the order of magnitude of `1e19`, this issue is unlikely to arise in the near future.

 But consider adding a check after [this line](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L91) to revert the call if `tokenId > type(uint64).max`.
 
 
 ## Non-critical
 ### [N‑01] Typos
 Correct the typos
 

 
 ```solidity
83:     ///         can register a fee receipient. @audit - recipient
 ```
 
  https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L83
 
 ### [N‑02] Redundant checks
`Turnstile.register()` checks the `_recipient` specified by the smart contract call is not the `address(0)`.

This check is [already performed](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/3ba2a1354f8f830d5a0e711537efdbdd8bcb109e/contracts/token/ERC721/ERC721.sol#L257) in `_mint()`, so it can be removed.
 
 
 ### [N‑03] `public` functions not called by the contract should be declared `external` instead
 
 4 instances of this issue:
 
 - [register()](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L86)
 - [assign()](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L107)
 - [withdraw()](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L127)
 - [distributeFees()](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148)
 
