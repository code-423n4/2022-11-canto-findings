# QA report

## Author: rotcivegaf

## Low Risk
| L-N    |Issue|Instances|
|:------:|:----|:-------:|
| [L&#x2011;01] | The `symbol` should be an abbreviation of the `name` | 2 |
| [L&#x2011;02] | Use `_safeMint()` instead of `_mint()` | 2 |


## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Event is missing `indexed` fields | 4 |
| [N&#x2011;02] | Functions not used internally could be marked `external` | 4 |
| [N&#x2011;03] | Typo | 3 |
| [N&#x2011;04] | Unused error | 1 |
| [N&#x2011;05] | The name of the contract file should be start with capital letter | 1 |
| [N&#x2011;06] | Old version of OpenZeppelin on `Canto` folder, and different between `Canto` and `CIP-001` folders | 1 |
| [N&#x2011;07] | Move the event emitting to the bottom of the function | 6 |
| [N&#x2011;08] | The `msg.sender` could be a EOA | 2 |


## Low Risk

### [L-01] The `symbol` should be an abbreviation of the `name`

As in the [eip-721](https://eips.ethereum.org/EIPS/eip-721) it says:
```solidity
    /// @notice An abbreviated name for NFTs in this contract
    function symbol() external view returns (string _symbol);
```
This could broke extensions like metamask or web like coinmarketcap, binance
The recommendation is to use from 3 to 5 letters for the `symbol`

```solidity
File: CIP-001/src/Turnstile.sol

57:    constructor() ERC721("Turnstile", "Turnstile") {}
```

```solidity
File: Canto/contracts/turnstile.sol

58:    constructor() ERC721("Turnstile", "Turnstile") {}
```

### [L-02] Use `_safeMint` rather than `_mint`

OpenZeppelin recommends the usage of `_safeMint()` instead of `_mint()`, if the `_recipient` is a contract, `_safeMint()` checks whether they can handle ERC721 tokens: ["WARNING: Usage of this method is discouraged, use {_safeMint} whenever possible"](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/376cef91ca330e66e0275424bb1035a6715c77f1/contracts/token/ERC721/ERC721.sol#L270)

If the `_recipient` of the `register` it's a contract this asset could be stuck on it, if the contract not be prepare to receive and manipulate the asset

```solidity
File: CIP-001/src/Turnstile.sol

92:        _mint(_recipient, tokenId);
```

```solidity
File: Canto/contracts/turnstile.sol

93:        _mint(_recipient, tokenId);
```

Recommend use `_safeMint` rather than `_mint`, this could bring the possibility of reentrancy attacks. Use reentrancy guard or move the mint to the end of the function

```solidity
    function register(address _recipient, bytes memory _data) public onlyUnregistered returns (uint256 tokenId) {
        address smartContract = msg.sender;

        if (_recipient == address(0)) revert InvalidRecipient();

        tokenId = _tokenIdTracker.current();
        _tokenIdTracker.increment();

        emit Register(smartContract, _recipient, tokenId);

        feeRecipient[smartContract] = NftData({
            tokenId: tokenId,
            registered: true
        });

        _safeMint(_recipient, tokenId, _data);
    }
```

## Non-critical

### [N‑01] Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be `indexed`.

```solidity
File: Canto/contracts/turnstile.sol

28:     event Register(address smartContract, address recipient, uint256 tokenId);

29:     event Assign(address smartContract, uint256 tokenId);

30:     event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);

31:     event DistributeFees(uint256 tokenId, uint256 feeAmount);
```

### [N‑02] Functions not used internally could be marked `external`

```solidity
File: Canto/contracts/turnstile.sol

 87:     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

108:     function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {

128:     function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)

149:     function distributeFees(uint256 _tokenId) public onlyOwner payable {
```

### [N‑03] Typo

```solidity
File: Canto/x/csr/keeper/evm_hooks.go

/// @audit: `acount` to `account`
75:		return sdkerrors.Wrapf(ErrFeeDistribution, "EVMHook::PostTxProcessing failed to distribute fees from fee collector to module acount, %d", err)
```

```solidity
File: Canto/x/csr/keeper/evm.go

/// @audit: `contructor` to `constructor`
34:// well as an arbitrary number of arguments which will be supplied to the contructor. All contracts deployed
```

```solidity
File: CIP-001/src/Turnstile.sol

/// @audit: `receipient` to `recipient`
83:    ///         can register a fee receipient.
```

```solidity
File: Canto/contracts/turnstile.sol

/// @audit: `receipient` to `recipient`
84:    ///         can register a fee receipient.
```

### [N‑04] Unused error

```solidity
File: Canto/contracts/turnstile.sol

34:    error NotSmartContract();
```

### [N‑05] The name of the contract file should be start with capital letter

```solidity
From: Canto/contracts/turnstile.sol

To:   Canto/contracts/Turnstile.sol
```

### [N‑06] Old version of OpenZeppelin on `Canto` folder, and different between `Canto` and `CIP-001` folders

The version of OpenZeppelin on `Canto` it's minimum the 4.4.2, and in the `CIP-001` it's 4.7.0
Update the version of `Canto` folder

```json
From: Canto/package.json

10:    "@openzeppelin/contracts": "^4.4.2"
```

```json
From: CIP-001/lib/openzeppelin-contracts/package.json

4:  "version": "4.7.0",
```

### [N‑07] Move the event emitting to the bottom of the function

```solidity
File: CIP-001/src/Turnstile.sol

 95:        emit Register(smartContract, _recipient, tokenId);

112:        emit Assign(smartContract, _tokenId);

139:        emit Withdraw(_tokenId, _recipient, _amount);
```

```solidity
File: Canto/contracts/turnstile.sol

 96:        emit Register(smartContract, _recipient, tokenId);

113:        emit Assign(smartContract, _tokenId);

140:        emit Withdraw(_tokenId, _recipient, _amount);
```

### [N‑08] The `msg.sender` could be a EOA

The `msg.sender` in the functions `register` and `assign` could be a EOA and not only smart contract
Consider add a check to ensure what the sender it's a smart contract:
- option1: `if (smartContract == tx.origin) revert NotSmartContract();`
- option2: [`isContract(address account)`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/b438cb695a1ac520cee6678610b161b1d5df4d9c/contracts/utils/Address.sol#L36-L42), note: if the call it's in the constructor this return `false`

```diff
File: CIP-001/src/Turnstile.sol

@@ -37,6 +37,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     error InvalidTokenId();
     error NothingToWithdraw();
     error NothingToDistribute();
+    error NotSmartContract();

     /// @dev only owner of _tokenId can call this function
     modifier onlyNftOwner(uint256 _tokenId) {

@@ -86,6 +87,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
         address smartContract = msg.sender;

+        if (smartContract == tx.origin) revert NotSmartContract();
         if (_recipient == address(0)) revert InvalidRecipient();

         tokenId = _tokenIdTracker.current();

@@ -107,6 +109,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
         address smartContract = msg.sender;

+        if (smartContract == tx.origin) revert NotSmartContract();
         if (!_exists(_tokenId)) revert InvalidTokenId();

         emit Assign(smartContract, _tokenId);
```

```solidity
File: Canto/contracts/turnstile.sol

@@ -38,6 +38,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     error InvalidTokenId();
     error NothingToWithdraw();
     error NothingToDistribute();
+    error NotSmartContract();

     /// @dev only owner of _tokenId can call this function
     modifier onlyNftOwner(uint256 _tokenId) {

@@ -87,6 +88,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
         address smartContract = msg.sender;

+        if (smartContract == tx.origin) revert NotSmartContract();
         if (_recipient == address(0)) revert InvalidRecipient();

         tokenId = _tokenIdTracker.current();

@@ -108,6 +110,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
         address smartContract = msg.sender;

+        if (smartContract == tx.origin) revert NotSmartContract();
         if (!_exists(_tokenId)) revert InvalidTokenId();

         emit Assign(smartContract, _tokenId);
```
