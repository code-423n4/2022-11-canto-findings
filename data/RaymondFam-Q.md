## Sanity Checks
Consider adding a zero address check for `_recipient` in `withdraw()` at the beginning of the code block. This is because `Address.sendValue()` only ensure that the 2300 gas limit is catered for.

Here is the instance entailed:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127-L144

Consider having the code block refactored as follows:

```
    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
        public
        onlyNftOwner(_tokenId)
        returns (uint256)
    {
        uint256 earnedFees = balances[_tokenId];

        if (_recipient == address(0)) revert InvalidRecipient(); // Zero Address Check
        if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();
        if (_amount > earnedFees) _amount = earnedFees;

        balances[_tokenId] = earnedFees - _amount;

        emit Withdraw(_tokenId, _recipient, _amount);

        Address.sendValue(_recipient, _amount);

        return _amount;
    }
```
## Early Checks
Checks should appear at the beginning of a function logic so that the function call would revert as early as possible if need be to minimize the wastage of gas. 

Here are the two instances entailed:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L89

```
        if (_recipient == address(0)) revert InvalidRecipient();
```
Consider reordering the above code line by moving it before line 87:

```
        if (_recipient == address(0)) revert InvalidRecipient();
        address smartContract = msg.sender;
```
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L110

```
        if (!_exists(_tokenId)) revert InvalidTokenId();
```
Consider also reordering the above code line by moving it before line 108:

```
        if (!_exists(_tokenId)) revert InvalidTokenId();
        address smartContract = msg.sende;
```
## Unneeded Cache
Caching a global variable, `msg.sender` is unnecessary as it has no gas saving benefit in doing so. Having the comments denoting `msg.sender` is assumed to be a smart contract is adequate enough to remove the unneeded cache.

Here are the three instances entailed:

[File: Turnstile.sol](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)

```
50:        address smartContract = msg.sender;

87:        address smartContract = msg.sender;

108:        address smartContract = msg.sender;
```