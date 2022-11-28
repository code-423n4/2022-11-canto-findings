> * Check  `!_exists` before declaring `address smartContract = msg.sender;` on Turnstile.sol#L110
> * Check `_amount ==0` before declaring earnedFees 
> 
```solidity
	uint256 earnedFees = balances[_tokenId];

	if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();
```
> * Rewrite code on Turnstile.sol#L135 as below also use unchecked as it can't underflow
```solidity
	unchecked{
		balances[_tokenId] = _amount > earnedFees ? 0 : earnedFees - _amount;
	}
```
