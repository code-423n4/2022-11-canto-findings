
# QA

## `_safeMint()` should be used rather than `_mint()` wherever possible
### Impact 
In `Turnstile.sol`, eventually it is called ERC721 `_mint()` at `register()`. Calling `_mint()` this way does not ensure that the receiver of the NFT is able to accept them, making possible to lose them. 

`_safeMint()` should be used with as it checks to see if a user can properly accept an NFT and reverts otherwise.

There is no check of the address provided by the mint NFT that it implements `ERC721Receiver`. 

#### Details 
`_mint()` is discouraged in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`. 

Both open OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

#### References
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271

### Github Permalinks

https://github.com/code-423n4/2022-11-canto/blob/162e0d2d957154c034cf5d24d402b0c9d55512c1/CIP-001/src/Turnstile.sol#L92
        _mint(_recipient, tokenId);

### Mitigation
Use `_safeMint()` as suggested by OpenZeppelin or include the check before minting.
However, consider the risk of reentrancies


## Single-step process for critical ownership transfer/renounce is risky
### Impact
Important functions like `distributeFees` are only usable by admins

Given that Turnstile is derived from `Ownable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.

### Github Permalinks
- `Ownable`
https://github.com/code-423n4/2022-11-canto/blob/162e0d2d957154c034cf5d24d402b0c9d55512c1/CIP-001/src/Turnstile.sol#L12
contract Turnstile is Ownable, ERC721Enumerable {

### Recommended steps
Consider using `Ownable2Step` from OZ

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.



# Informational
## Return value is not being used and can be removed
### Impact
In `withdraw` return value is not being used, so it can be removed. 

### Github Permalinks
https://github.com/code-423n4/2022-11-canto/blob/162e0d2d957154c034cf5d24d402b0c9d55512c1/CIP-001/src/Turnstile.sol#L127-L144

### Mitigation
Remove return value
