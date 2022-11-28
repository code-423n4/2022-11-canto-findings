

## [L-01] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
Turnstile.sol::92 => _mint(_recipient, tokenId);
```

## [N-01] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
Turnstile.sol::28 => event Register(address smartContract, address recipient, uint256 tokenId);
Turnstile.sol::29 => event Assign(address smartContract, uint256 tokenId);
Turnstile.sol::30 => event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);
Turnstile.sol::31 => event DistributeFees(uint256 tokenId, uint256 feeAmount);
```

## [N-02] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

```
Turnstile.sol::107 => function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
Turnstile.sol::148 => function distributeFees(uint256 _tokenId) public onlyOwner payable {
```
