## [Low-01] Turnstile: register() will mint NFT for EOA


### Impact

Turnstile.register() does not restrict the caller to smart contracts and will mint NFTs for the _recipient specified for the caller.
This allows the EOA to call Turnstile.register and mint NFTs.
Although the ValidateContract function is called in the RegisterEvent function to prevent the EOA from registering, the fact that the EOA can mint NFTs breaks the developer's assumption.

```
    /// @notice Mints ownership NFT that allows the owner to collect fees earned by the smart contract.
    ///         `msg.sender` is assumed to be a smart contract that earns fees. Only smart contract itself
    ///         can register a fee receipient.
    /// @param _recipient recipient of the ownership NFT
    /// @return tokenId of the ownership NFT that collects fees
    function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
```
### Proof of Concept
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L82-L102
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/x/csr/keeper/event_handler.go#L16-L25
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/x/csr/keeper/event_handler.go#L95-L110


### Recommended Mitigation Steps

Check msg.sender.code.length > 0 in Turnstile.register()


## [Low-02] _safeMint() should be used rather than _mint() wherever possible

### Impact
_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver.


### Proof of Concept
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L87-L93

### Recommended Mitigation Steps

Use _safeMint instead of _mint