# Gas report

## Author: rotcivegaf

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | Use assembly to check for `address(0)` | 1 |
| [G&#x2011;02] | Use directly `msg.sender` | 6 |
| [G&#x2011;03] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 4 |
| [G&#x2011;04] | Avoid `Address.sendValue` of OpenZeppelin | 2 |
| [G&#x2011;05] | Avoid `Counters` of OpenZeppelin | 2 |


## [G-01] Use assembly to check for `address(0)`

```solidity
File: Canto/contracts/turnstile.sol

90:         if (_recipient == address(0)) revert InvalidRecipient();
```

## [G-02] Use directly `msg.sender`

```solidity
File: CIP-001/src/Turnstile.sol

 50:        address smartContract = msg.sender;

 87:        address smartContract = msg.sender;

108:        address smartContract = msg.sender;
```

```solidity
File: Canto/contracts/turnstile.sol

 51:        address smartContract = msg.sender;

 88:        address smartContract = msg.sender;

109:        address smartContract = msg.sender;
```

## [G-03] Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement

`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```solidity
File: CIP-001/src/Turnstile.sol

/// @audit: If `_amount` is greater than `earnedFees` in the L135 assign `earnedFees` to `_amount` => at most the `earnedFees` and `_amount` are equal
137;        balances[_tokenId] = earnedFees - _amount;

/// @audit: impossibly overflow
151;        balances[_tokenId] += msg.value;
```

```solidity
File: Canto/contracts/turnstile.sol

/// @audit: If `_amount` is greater than `earnedFees` in the L136 assign `earnedFees` to `_amount` => at most the `earnedFees` and `_amount` are equal
138;        balances[_tokenId] = earnedFees - _amount;

/// @audit: impossibly overflow
152;        balances[_tokenId] += msg.value;
```

## [G-04] Avoid `Address.sendValue` of OpenZeppelin

Use directly `call` to send ether:
```solidity
    (bool success, ) = _recipient.call{value: _amount}("");
    if (!success) revert TransferFailed();
```
This save the gas of [`require(address(this).balance >= amount, "Address: insufficient balance");`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/b438cb695a1ac520cee6678610b161b1d5df4d9c/contracts/utils/Address.sol#L61) check of the OpenZeppelin library Address

```solidity
File: CIP-001/src/Turnstile.sol

141:        Address.sendValue(_recipient, _amount);
```

```solidity
File: Canto/contracts/turnstile.sol

142:        Address.sendValue(_recipient, _amount);
```

## [G-05] Struct packing

If change the `tokenId` from `uint256` to `uint248`, the struct reduce the gas cost from 2 slots to 1 slot

```solidity
File: CIP-001/src/Turnstile.sol

15:    struct NftData {
16:        uint256 tokenId;
17:        bool registered;
18:    }
```

```solidity
File: Canto/contracts/turnstile.sol

15:    struct NftData {
16:        uint256 tokenId;
17:        bool registered;
18:    }
```

Recommended Mitigation:

```diff
@@ -13,7 +13,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
     using Counters for Counters.Counter;

     struct NftData {
-        uint256 tokenId;
+        uint248 tokenId;
         bool registered;
     }

@@ -95,7 +95,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
         emit Register(smartContract, _recipient, tokenId);

         feeRecipient[smartContract] = NftData({
-            tokenId: tokenId,
+            tokenId: uint248(tokenId),
             registered: true
         });
     }

@@ -112,7 +112,7 @@ contract Turnstile is Ownable, ERC721Enumerable {
         emit Assign(smartContract, _tokenId);

         feeRecipient[smartContract] = NftData({
-            tokenId: _tokenId,
+            tokenId: uint248(_tokenId),
             registered: true
         });
```
