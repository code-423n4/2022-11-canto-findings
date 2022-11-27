## Low

### 

## Non-critical

### Unnecessary owner check

It doesn't seems to do any harm if this function is permissionless, then the contract don't even need `Ownable`

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148-L149

```solidity
    function distributeFees(uint256 _tokenId) public onlyOwner payable {

```

### Events lack indexed field

Add indexed fields for easier analytics

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L28-L31

```solidity
    event Register(address smartContract, address recipient, uint256 tokenId);
    event Assign(address smartContract, uint256 tokenId);
    event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);
    event DistributeFees(uint256 tokenId, uint256 feeAmount);
```