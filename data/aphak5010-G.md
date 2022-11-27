# Canto Gas Optimization Findings
## Summary
The Gas savings are calculated using the `forge test --gas-report` command.  
The same tests were used that are provided in the contest repository.

| Issue      | Title | File | Instances | Estimated Gas Savings (Deployments) | Estimated Gas Savings (Method calls) |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| G-00 | Do not save `msg.sender` to variable | Turnstile.sol | 3 | 8414 | 292 |

## [G-00] Do not save `msg.sender` to variable
There are 3 instances where `msg.sender` is saved to a memory variable in order to be used later.  

However this approach only saves Gas for storage variables. In the case of `msg.sender` it actually costs less gas to use `msg.sender` directly.  

Fix:  
```solidity
@@ -47,9 +47,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
 
     /// @dev only smart contract that is unregistered can call this function
     modifier onlyUnregistered() {
-        address smartContract = msg.sender;
-
-        if (isRegistered(smartContract)) revert AlreadyRegistered();
+        if (isRegistered(msg.sender)) revert AlreadyRegistered();
 
         _;
     }
@@ -84,17 +82,15 @@ contract Turnstile is Ownable, ERC721Enumerable {
     /// @param _recipient recipient of the ownership NFT
     /// @return tokenId of the ownership NFT that collects fees
     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
-        address smartContract = msg.sender;
-
         if (_recipient == address(0)) revert InvalidRecipient();
 
         tokenId = _tokenIdTracker.current();
         _mint(_recipient, tokenId);
         _tokenIdTracker.increment();
 
-        emit Register(smartContract, _recipient, tokenId);
+        emit Register(msg.sender, _recipient, tokenId);
 
-        feeRecipient[smartContract] = NftData({
+        feeRecipient[msg.sender] = NftData({
             tokenId: tokenId,
             registered: true
         });
@@ -105,13 +101,11 @@ contract Turnstile is Ownable, ERC721Enumerable {
     /// @param _tokenId tokenId which will collect fees
     /// @return tokenId of the ownership NFT that collects fees
     function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
-        address smartContract = msg.sender;
-
         if (!_exists(_tokenId)) revert InvalidTokenId();
 
-        emit Assign(smartContract, _tokenId);
+        emit Assign(msg.sender, _tokenId);
 
-        feeRecipient[smartContract] = NftData({
+        feeRecipient[msg.sender] = NftData({
             tokenId: _tokenId,
             registered: true
         });
```


Deployment Gas saved: **8414**  
Function call Gas saved (all functions): **292**  

