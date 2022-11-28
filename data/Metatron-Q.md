Non-critical
Not homogeneous line separation use

Throughout `Turnstile.sol` there is a jump back and forth between single and double line separation,
with no clear `logic block` to justify separating with with different line separation.

example:

172:
```
    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
        public
        onlyNftOwner(_tokenId)
        returns (uint256)
    {
        uint256 earnedFees = balances[_tokenId];

        if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();
        if (_amount > earnedFees) _amount = earnedFees;

        balances[_tokenId] = earnedFees - _amount;

        emit Withdraw(_tokenId, _recipient, _amount);

        Address.sendValue(_recipient, _amount);

        return _amount;
    }

    /// @notice Distributes collected fees to the smart contract. Only callable by owner.
    /// @param _tokenId NFT that earned fees
    function distributeFees(uint256 _tokenId) public onlyOwner payable {
        if (msg.value == 0) revert NothingToDistribute();

        balances[_tokenId] += msg.value;
        emit DistributeFees(_tokenId, msg.value);
    }
```