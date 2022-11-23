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