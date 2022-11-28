1. Reorder event 

reorder event for good readibilty, best practice for this case was :

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L30
```
 event Withdraw(address recipient, uint256 tokenId,  uint256 feeAmount);
```

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L127
```
function withdraw(address payable _recipient, uint256 _tokenId, uint256 _amount)
```

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L139
```
emit Withdraw(_recipient, _tokenId, _amount);
```

lastly change:
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L11
```
`withdraw(address,uint256,uint256)`
```


2. Missing Indexed 

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L30 

address indexed recipient

