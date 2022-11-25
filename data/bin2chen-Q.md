1. distributeFees() no check _tokenId exists,  maybe lose fees
```
    function distributeFees(uint256 _tokenId) public onlyOwner payable {
        if (msg.value == 0) revert NothingToDistribute();

        balances[_tokenId] += msg.value; //****@audit not check _tokenId exists ***/
        emit DistributeFees(_tokenId, msg.value);
    }
```
Recommendation:
```
    function distributeFees(uint256 _tokenId) public onlyOwner payable {
        if (msg.value == 0) revert NothingToDistribute();
+      if (!_exists(_tokenId)) revert InvalidTokenId();

        balances[_tokenId] += msg.value; //****@audit not check _tokenId exists ***/
        emit DistributeFees(_tokenId, msg.value);
    }
```

2. register() use unsafe _mint() suggest use _safeMint() to check _checkOnERC721Received
```
    function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
        address smartContract = msg.sender;

        if (_recipient == address(0)) revert InvalidRecipient();

        tokenId = _tokenIdTracker.current();
-       _mint(_recipient, tokenId);
+      _safeMint(_recipient, tokenId);

        _tokenIdTracker.increment();

        emit Register(smartContract, _recipient, tokenId);

        feeRecipient[smartContract] = NftData({
            tokenId: tokenId,
            registered: true
        });
    }
```