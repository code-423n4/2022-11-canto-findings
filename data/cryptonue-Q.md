# [L] _SAFEMINT() SHOULD BE USED RATHER THAN _MINT() WHEREVER POSSIBLE

`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements IERC721Receiver. Both `OpenZeppelin` and `solmate` have versions of this function

```solidity
File: Turnstile.sol
92:         _mint(_recipient, tokenId);
```



# [L] USE TWO-PHASE OWNERSHIP TRANSFERS

`Turnstile.sol` using Ownable contract from `openzeppelin/access/Ownable.sol` which doesn't include an accept function for accepting the transfer ownership.

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call accept() to become the new owner. This prevents passing the ownership to an account that is unable to use it.

# [L] No check of `_recipient` address on withdrawal

for the 3 parameters on `withdrawal` function, `tokenId` and `amount` are being checked, but not the `recipient` address. Just in case a user input 0x0 address, it will continue the function. Usually it need to be checked if the address is address(0). This to prevent sending msg.value (asset) / ether to zero address.