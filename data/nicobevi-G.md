## Remove auxiliar variable `smartContract` to save gas

### Where 

- https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/src/Turnstile.sol#L49-L55

  ```solidity
  modifier onlyUnregistered() {
    address smartContract = msg.sender;

    if (isRegistered(smartContract)) revert AlreadyRegistered();

    _;
  }
  ```

  Change de code to:

  ```solidity
  modifier onlyUnregistered() {
    if (isRegistered(msg.sender)) revert AlreadyRegistered();

    _;
  }
  ```

- https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/src/Turnstile.sol#L86-L101

  ```solidity
  function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
      address smartContract = msg.sender;

      if (_recipient == address(0)) revert InvalidRecipient();

      tokenId = _tokenIdTracker.current();
      _mint(_recipient, tokenId);
      _tokenIdTracker.increment();

      emit Register(smartContract, _recipient, tokenId);

      feeRecipient[smartContract] = NftData({
          tokenId: tokenId,
          registered: true
      });
  }
  ```

  Change the code to:

  ```solidity
  function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
    if (_recipient == address(0)) revert InvalidRecipient();

    tokenId = _tokenIdTracker.current();
    _mint(_recipient, tokenId);
    _tokenIdTracker.increment();

    emit Register(msg.sender, _recipient, tokenId);

    feeRecipient[msg.sender] = NftData({
        tokenId: tokenId,
        registered: true
    });
  }
  ```

- https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/src/Turnstile.sol#L107-L120

  ```solidity
  function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
      address smartContract = msg.sender;

      if (!_exists(_tokenId)) revert InvalidTokenId();

      emit Assign(smartContract, _tokenId);

      feeRecipient[smartContract] = NftData({
          tokenId: _tokenId,
          registered: true
      });

      return _tokenId;
  }
  ```

  Change the code to:

  ```solidity
  function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
      if (!_exists(_tokenId)) revert InvalidTokenId();

      emit Assign(msg.sender, _tokenId);

      feeRecipient[msg.sender] = NftData({
          tokenId: _tokenId,
          registered: true
      });

      return _tokenId;
  }
  ```

Those changes will save 17082 gas units on tests.

## Avoid double storage reading on `getTokenId()`

`reeRecipient[_smartContract]` is being read twice from storage (one on `isRegistered()` call and the other one on the return expresion) unnecesarly wasting gas.

### Where

https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/src/Turnstile.sol#L68-L72

```solidity
function getTokenId(address _smartContract) external view returns (uint256) {
    if (!isRegistered(_smartContract)) revert Unregistered();

    return feeRecipient[_smartContract].tokenId;
}
```

Change the code to

```solidity
function getTokenId(address _smartContract) external view returns (uint256) {
    NftData memory data = feeRecipient[_smartContract];
    
    if (!data.registered) revert Unregistered();

    return data.tokenId;
}
```

That will save more than 22000 gas units.


## Use unchecked on safe arithmetic operations

### Where

* https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/  src/Turnstile.sol#L137

  ```solidity
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
  ```

  Change the code to

  ```solidity
    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
        public
        onlyNftOwner(_tokenId)
        returns (uint256)
    {
        uint256 earnedFees = balances[_tokenId];

        if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();

        if (_amount > earnedFees) _amount = earnedFees;

        unchecked {
          balances[_tokenId] = earnedFees - _amount;
        }

        emit Withdraw(_tokenId, _recipient, _amount);

        Address.sendValue(_recipient, _amount);

        return _amount;
    }
  ```

* https://github.com/code-423n4/2022-11-canto/blob/f6908b3453560ec25f2362741b3287fa312a3ff7/CIP-001/src/Turnstile.sol#L137

  ```solidity
    function distributeFees(uint256 _tokenId) public onlyOwner payable {
        if (msg.value == 0) revert NothingToDistribute();

        balances[_tokenId] += msg.value;
        emit DistributeFees(_tokenId, msg.value);
    }
  ```

  Change the code to

  ```solidity
    function distributeFees(uint256 _tokenId) public onlyOwner payable {
        if (msg.value == 0) revert NothingToDistribute();

        unchecked {
          balances[_tokenId] += msg.value;
        }

        emit DistributeFees(_tokenId, msg.value);
    }
  ```

## Remove `onlyNftOwner` only used one and move the validation to the function `withdraw()`

If the modifier is used just once then the logic can be moved to the callee function instead and save some gas.

That will save 37 gas units.