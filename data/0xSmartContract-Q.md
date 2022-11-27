## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| SupportInterface of `ERC721Enumerable` is missing | 1 |
|[L-02]| `onlyOwner` privileges can cause damage of the project in a possible privateKey exploit | 1 |
|[L-03]|Use ```safeTransferOwnership``` instead of ```transferOwnership``` function | 1 |
|[L-04]| Owner can renounce Ownership| 1 |
|[L-05]| Critical Address Changes Should Use Two-step Procedure | 1 |
|[L-06]| Missing Re-Entrancy Guard to `withdraw` function | 1 |
|[L-07]| No Check if OnErc721Received is implemented| 1 |

Total 7 issues
### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01]|For modern and more readable code; update import usages|1|
| [NC-02] |NatSpec is missing  |1|
| [NC-03] |No same value input control| 1 |
| [NC-04] |`0 address` check for ` asset ` | 1 |
| [NC-05] |`Function writing` that does not comply with the `Solidity Style Guide` | 1|
| [NC-06] |Add a timelock to critical functions  | 1 |
| [NC-07] |Take advantage of Custom Error's return value property | 1 |
| [NC-08] |Using Vulnerable Version of Openzeppelin| 1 |
| [NC-09] |Import only the parts you use |1 |

Total 9 issues
### [L-01] SupportInterface of `ERC721Enumerable` is missing

[Turnstile.sol#L12](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L12)

To adapt the way the contract interacts, supportInterface with EIP165 is sometimes used to query whether a contract supports the interface and if yes which version of the interface

https://eips.ethereum.org/EIPS/eip-165



### Recommendation;
Add this code for Enumerable supportInterface

```solidity
 function supportsInterface(bytes4 interfaceId)
         public
         view
         override(ERC721, ERC721Enumerable)
         returns (bool)
     {
         return super.supportsInterface(interfaceId);
     }
```


```js
ERC165               0x01ffc9a7
ERC721               0x80ac58cd
ERC721Metadata       0x5b5e139f
ERC721TokenReceiver  0x150b7a02
ERC721Enumerable     0x780e9d63
AccessControl        0x7965db0b

```
### [L-02] Very critical `onlyOwner` privileges can cause damage of the project in a possible privateKey exploit

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148

### Vulnerability details
Typically, the contract’s `onlyOwner` is the account that deploys the contract. As a result, the `onlyOwner` is able to perform certain privileged activities.

However, `onlyOwner` privileges are numerous and there is no timelock structure in the process of using these privileges.
The onlyOwner  is assumed to be an EOA, since the documents do not provide information on whether the `onlyOwner` will be a multisign structure.

In parallel with the private key thefts of the project onlyOwners, which have increased recently, this vulnerability has been stated as medium

Similar vulnerability;
Private keys stolen:

Hackers have stolen cryptocurrency worth around €552 million from a blockchain project linked to the popular online game Axie Infinity, in one of the largest cryptocurrency heists on record. Security issue : PrivateKey of the project officer was stolen:
https://www.euronews.com/next/2022/03/30/blockchain-network-ronin-hit-by-552-million-crypto-heist


### Proof of Concept

`onlyonlyOwner` powers;
```js

Canto/contracts/turnstile.sol:
  146  
  147:     /// @notice Distributes collected fees to the smart contract. Only callable by owner.
  148:     /// @param _tokenId NFT that earned fees
  149:     function distributeFees(uint256 _tokenId) public onlyOwner payable {
  150          if (msg.value == 0) revert NothingToDistribute();

```

onlyOwner can also make this functionality unavailable by using the renounceOwnership property


### Recommendation;

1- A timelock contract should be added to use `onlyOwner` privileges. In this way, users can be warned in case of a possible security weakness.
2- `onlyOwner` can be a Multisign wallet and this part is specified in the documentation

### [L-03] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
[Turnstile.sol#L4](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L4)

**Description:**
```transferOwnership``` function is used to change Ownership from ```Ownable.sol```.

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)
### [L-04] Owner can renounce Ownership

**Context:**
[Turnstile.sol#L4](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L4)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Openzeppelin’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

`onlyOwner` functions;
```js

Canto/contracts/turnstile.sol:
  146  
  147:     /// @notice Distributes collected fees to the smart contract. Only callable by owner.
  148:     /// @param _tokenId NFT that earned fees
  149:     function distributeFees(uint256 _tokenId) public onlyOwner payable {
  150          if (msg.value == 0) revert NothingToDistribute();

```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.
### [L-05] Critical Address Changes Should Use Two-step Procedure

The critical procedures should be two step process.


```solidity
Canto/contracts/turnstile.sol:
   86      /// @return tokenId of the ownership NFT that collects fees
   87:     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
   88:         address smartContract = msg.sender;
   89: 
   90:         if (_recipient == address(0)) revert InvalidRecipient();
   91: 
   92:         tokenId = _tokenIdTracker.current();
   93:         _mint(_recipient, tokenId);
   94:         _tokenIdTracker.increment();
   95: 
   96:         emit Register(smartContract, _recipient, tokenId);
   97: 
   98:         feeRecipient[smartContract] = NftData({
   99:             tokenId: tokenId,
  100:             registered: true
  101:         });
  102:     }


```

Recommended Mitigation Steps
Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.


### [L-06] Missing Re-Entrancy Guard to `withdraw` function

[Turnstile.sol#L141](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L141)

Ether transfer is done with `Address.sendValue(_recipient, _amount);` in the `withdraw` function, OZ's Address.sol library is used

```solidity
Canto/contracts/turnstile.sol:
  127      /// @return amount of fees withdrawn
  128:     function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
  129:         public
  130:         onlyNftOwner(_tokenId)
  131:         returns (uint256)
  132:     {
  133:         uint256 earnedFees = balances[_tokenId];
  134: 
  135:         if (earnedFees == 0 || _amount == 0) revert NothingToWithdraw();
  136:         if (_amount > earnedFees) _amount = earnedFees;
  137: 
  138:         balances[_tokenId] = earnedFees - _amount;
  139: 
  140:         emit Withdraw(_tokenId, _recipient, _amount);
  141: 
  142:         Address.sendValue(_recipient, _amount);
  143: 
  144:         return _amount;
  145:     }

```

There is this warning in OZ's Address.sol library and. Accordingly, he used the Check-Effect-Interaction pattern in the project

```
* IMPORTANT: because control is transferred to `recipient`, care must be
      * taken to not create reentrancy vulnerabilities. Consider using
      * {ReentrancyGuard} or the
      * https://solidity.readthedocs.io/en/v0.5.11/security-considerations.html#use-the-checks-effects-interactions-pattern[checks-effects-interactions pattern].
      */
```

It would be best practice to use re-entrancy Guard for reasons such as complicated dangers such as view Re-Entrancy that emerged in the last period and the possibility of expanding the project and its integration with other contracts

https://twitter.com/lmount_/status/1577307245276459011?t=XSvtIkum8nuzI8ZM7lfq-A&s=19
https://github.com/euler-xyz/euler-contracts/commit/91adeee39daf8ece00584b6f7ec3e60a1d226bc9
### [L-07] No Check if OnErc721Received is implemented

`Turnstile.register()` will mint a NFT . When minting a NFT, the function does not check if a receiving contract implements onERC721Received().
_recipient can be contract address.

The intention behind this function is to check if the address receiving the NFT, if it is a contract, implements onERC721Received(). Thus, there is no check whether the receiving address supports ERC-721 tokens and position could be not transferrable in some cases.

Following shows that _mint is used instead of _safeMint

```solidity
Canto/contracts/turnstile.sol:
   86      /// @return tokenId of the ownership NFT that collects fees
   87:     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
   88:         address smartContract = msg.sender;
   89: 
   90:         if (_recipient == address(0)) revert InvalidRecipient();
   91: 
   92:         tokenId = _tokenIdTracker.current();
   93:         _mint(_recipient, tokenId);
   94:         _tokenIdTracker.increment();
   95: 
   96:         emit Register(smartContract, _recipient, tokenId);
   97: 
   98:         feeRecipient[smartContract] = NftData({
   99:             tokenId: tokenId,
  100:             registered: true
  101:         });
  102:     }


```

Recommendation
Consider using _safeMint instead of _mint



### [NC-01] For modern and more readable code; update import usages

**Context:**
All contracts

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```
### [NC-02] NatSpec is missing 

**Description:**
NatSpec is missing for the following functions , constructor and modifier:

**Context:**

```solidity

CIP-001/src/Turnstile.sol:
  18  
  19:     struct NftData {
  20:         uint256 tokenId;
  21:         bool registered;
  22:     }
  23: 
  24:     Counters.Counter private _tokenIdTracker;

  32:     event Register(address smartContract, address recipient, uint256 tokenId);
  33:     event Assign(address smartContract, uint256 tokenId);
  34:     event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);
  35:     event DistributeFees(uint256 tokenId, uint256 feeAmount);

  61:     constructor() ERC721("Turnstile", "Turnstile") {}


```

### [NC-03] No same value input control


```solidity
CIP-001/src/Turnstile.sol:
   89      /// @return tokenId of the ownership NFT that collects fees
   90:     function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
   91:         address smartContract = msg.sender;
   92: 
   93:         if (_recipient == address(0)) revert InvalidRecipient();
   94: 
   95:         tokenId = _tokenIdTracker.current();
   96:         _mint(_recipient, tokenId);
   97:         _tokenIdTracker.increment();
   98: 
   99:         emit Register(smartContract, _recipient, tokenId);
  100: 
  101:         feeRecipient[smartContract] = NftData({
  102:             tokenId: tokenId,
  103:             registered: true
  104:         });
  105:     }

```
### [NC-04] `0 address` check for ` asset `

**Context:**

```solidity
CIP-001/src/Turnstile.sol:
  144  
  145:         Address.sendValue(_recipient, _amount);

```

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (_recipient == address(0)) revert ADDRESS_ZERO();`

### [NC-05] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [NC-06] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L12
[Turnstile.sol#L12](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L12)

Imported contracts contain critical functions (TransferOwnerShip, RenounOwnerShip etc.) adding timelock to them

### [N-07] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.

For Example;

```diff
CIP-001/src/Turnstile.sol:
  110      /// @return tokenId of the ownership NFT that collects fees
  111:     function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
  112:         address smartContract = msg.sender;
  113: 
- 114:         if (!_exists(_tokenId)) revert InvalidTokenId();
+ 114:         if (!_exists(_tokenId)) revert InvalidTokenId(_tokenId);

```

### [N-08] Using Vulnerable Version of Openzeppelin

The package.json configuration file says that the project is using 4.7.0 of OpenZeppelin which has a vulnerability

Although there is no security vulnerability covering the project, it is recommended to use the latest version 4.7.3

```js
CIP-001/lib/openzeppelin-contracts/package.json:
  1: {
  2:   "name": "openzeppelin-solidity",
  3:   "description": "Secure Smart Contract library for Solidity",
  4:   "version": "4.7.0",

```
### [N-09] Import only the parts you use

Instead of importing all of the imported contracts, import only the parts you use. This both eliminates a security risk and is a better method for transparency for those who read and audit the contract.

```js
Canto/x/csr/keeper/csr.go:
   2  
   3: import (
   4: 	"encoding/binary"
   5: 
   6: 	"github.com/Canto-Network/Canto/v2/x/csr/types"
   7: 	"github.com/cosmos/cosmos-sdk/store/prefix"
   8: 	sdk "github.com/cosmos/cosmos-sdk/types"
   9: 	"github.com/ethereum/go-ethereum/common"
  10: )

```
