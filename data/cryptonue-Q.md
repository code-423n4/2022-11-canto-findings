# [L] USE TWO-PHASE OWNERSHIP TRANSFERS

`Turnstile.sol` using Ownable contract from `openzeppelin/access/Ownable.sol` which doesn't include an accept function for accepting the transfer ownership.

Consider adding a two-phase transfer, where the current owner nominates the next owner, and the next owner has to call accept() to become the new owner. This prevents passing the ownership to an account that is unable to use it.
