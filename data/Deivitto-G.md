# GAS
## Operation can be `unchecked`
### Summary
A `a - b` operation already checked for `b` not being able to be bigger than `a` can be unchecked in order to save gas as it can't underflow 
### Github Permalink
https://github.com/code-423n4/2022-11-canto/blob/162e0d2d957154c034cf5d24d402b0c9d55512c1/CIP-001/src/Turnstile.sol#L135-L137 
### Recommendation
Add `unchecked` to 
`        balances[_tokenId] = earnedFees - _amount;`

## Operation can be considered for `unchecked`
### Summary
Operation is most likely to not cause overflow
### Impact
`distributeFees` is a `onlyOwner` operation
For overflowing it needs to reach the value of `type(uint256).max` what is:
`115792089237316195423570985008687907853269984665640564039457584007913129639935`
The way for `balances[_tokenId]` to be increased is by calling this function that only owners can call, what uses `msg.value` for overflowing
It's most likely no admin calls the function with that `msg.value` so it can be `unchecked` in order to save gas

### Github Permalink
https://github.com/code-423n4/2022-11-canto/blob/162e0d2d957154c034cf5d24d402b0c9d55512c1/CIP-001/src/Turnstile.sol#L151

### Recommendation
Consider using `unchecked` for 
`balances[_tokenId] += msg.value;`

