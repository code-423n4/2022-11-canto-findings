# [L] USE TWO-PHASE OWNERSHIP TRANSFERS

`Turnstile.sol` using Ownable contract from `openzeppelin/access/Ownable.sol` which doesn't include an accept function for accepting the transfer ownership.

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call accept() to become the new owner. This prevents passing the ownership to an account that is unable to use it.

# [L] No check of `_recipient` address on withdrawal

for the 3 parameters on `withdrawal` function, `tokenId` and `amount` are being checked, but not the `recipient` address. Just in case a user input 0x0 address, it will continue the function. Usually it need to be checked if the address is address(0). This to prevent sending msg.value (asset) / ether to zero address.