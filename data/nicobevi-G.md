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

## The struct NftData is not necessary if Counter is removed and a uint256 initialized on 1 is used instead

Instead of a complex struct with a boolean to validate that the contract has a nft associated what we can do is initialize our tokenId counter on 1 and use just a uint256 => uint256. If the tokenId is 0 then that contract doesn't have a nft associated (aka registered is false)

This would imply big changes on the contract logic (and tests) but it would reduce the gas consumption drastically (-6315270 gas units on tests)

### Changes

```solidity
  // SPDX-License-Identifier: GPLv3
  pragma solidity 0.8.17;

  import "openzeppelin/access/Ownable.sol";
  import "openzeppelin/token/ERC721/extensions/ERC721Enumerable.sol";
  // import "openzeppelin/utils/Counters.sol"; REMOVE THIS LINE

  /// @notice Implementation of CIP-001 https://github.com/Canto-Improvement-Proposals/CIPs/blob/main/CIP-001.md
  /// @dev Every contract is responsible to register itself in the constructor by calling `register(address)`.
  ///      If contract is using proxy pattern, it's possible to register retroactively, however past fees will be lost.
  ///      Recipient withdraws fees by calling `withdraw(uint256,address,uint256)`.
  contract Turnstile is Ownable, ERC721Enumerable {
      // using Counters for Counters.Counter; REMOVE THIS LINE

      // REMOVE THE STRUCT
      /*
      struct NftData {
          uint256 tokenId;
          bool registered;
      }
      */

      // REPLACE THIS LINE WITH LINE BELOW
      // Counters.Counter private _tokenIdTracker;
      uint256 private _tokenIdTracker = 1;

      /// @notice maps smart contract address to tokenId
      // REPLACE THIS LINE WITH LINE BELOW
      // mapping(address => NftData) public feeRecipient;
      mapping(address => uint256) public feeRecipient;

      /// @notice maps tokenId to fees earned
      mapping(uint256 => uint256) public balances;

      event Register(address smartContract, address recipient, uint256 tokenId);
      event Assign(address smartContract, uint256 tokenId);
      event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);
      event DistributeFees(uint256 tokenId, uint256 feeAmount);

      error NotAnOwner();
      error AlreadyRegistered();
      error Unregistered();
      error InvalidRecipient();
      error InvalidTokenId();
      error NothingToWithdraw();
      error NothingToDistribute();

      /// @dev only owner of _tokenId can call this function
      modifier onlyNftOwner(uint256 _tokenId) {
          if (ownerOf(_tokenId) != msg.sender) revert NotAnOwner();

          _;
      }

      /// @dev only smart contract that is unregistered can call this function
      modifier onlyUnregistered() {
          address smartContract = msg.sender;

          if (isRegistered(smartContract)) revert AlreadyRegistered();

          _;
      }

      constructor() ERC721("Turnstile", "Turnstile") {}

      /// @notice Returns current value of counter used to tokenId of new minted NFTs
      /// @return current counter value
      function currentCounterId() external view returns (uint256) {
          // REPLACE LINE
          // return _tokenIdTracker.current();
          return _tokenIdTracker;
      }

      /// @notice Returns tokenId that collects fees generated by the smart contract
      /// @param _smartContract address of the smart contract
      /// @return tokenId that collects fees generated by the smart contract
      function getTokenId(address _smartContract) external view returns (uint256) {
          if (!isRegistered(_smartContract)) revert Unregistered();

          // REPLACE LINE
          // return feeRecipient[_smartContract].tokenId;
          return feeRecipient[_smartContract];
      }

      /// @notice Returns true if smart contract is registered to collect fees
      /// @param _smartContract address of the smart contract
      /// @return true if smart contract is registered to collect fees, false otherwise
      function isRegistered(address _smartContract) public view returns (bool) {
          // REPLACE LINE
          // return feeRecipient[_smartContract].registered;
          return feeRecipient[_smartContract] != 0;
      }

      /// @notice Mints ownership NFT that allows the owner to collect fees earned by the smart contract.
      ///         `msg.sender` is assumed to be a smart contract that earns fees. Only smart contract itself
      ///         can register a fee receipient.
      /// @param _recipient recipient of the ownership NFT
      /// @return tokenId of the ownership NFT that collects fees
      function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
          address smartContract = msg.sender;

          if (_recipient == address(0)) revert InvalidRecipient();

          // REPLACE LINE
          // tokenId = _tokenIdTracker.current();
          tokenId = _tokenIdTracker;

          _mint(_recipient, tokenId);
          
          // REPLACE LINE
          // _tokenIdTracker.increment();
          unchecked {
            ++_tokenIdTracker;
          }

          emit Register(smartContract, _recipient, tokenId);

          // REPLACE LINE
          // feeRecipient[smartContract] = NftData({
          //     tokenId: tokenId,
          //     registered: true
          // });

          feeRecipient[smartContract] = tokenId;
      }

      /// @notice Assigns smart contract to existing NFT. That NFT will collect fees generated by the smart contract.
      ///         Callable only by smart contract itself.
      /// @param _tokenId tokenId which will collect fees
      /// @return tokenId of the ownership NFT that collects fees
      function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
          address smartContract = msg.sender;

          if (!_exists(_tokenId)) revert InvalidTokenId();

          emit Assign(smartContract, _tokenId);

          // REPLACE LINE
          // feeRecipient[smartContract] = NftData({
          //     tokenId: _tokenId,
          //     registered: true
          // });
          feeRecipient[smartContract] = _tokenId;

          return _tokenId;
      }

      /// @notice Withdraws earned fees to `_recipient` address. Only callable by NFT owner.
      /// @param _tokenId token Id
      /// @param _recipient recipient of fees
      /// @param _amount amount of fees to withdraw
      /// @return amount of fees withdrawn
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
  }
```

Also there are some changes to be made on `Turnstile.t.sol`

* Lines `assertEq(turnstile.currentCounterId(), 0);` should be changed to `assertEq(turnstile.currentCounterId(), 1);`
* Line 45 should be changed from `assertEq(turnstile.currentCounterId(), i);` to `assertEq(turnstile.currentCounterId(), i + 1);`
* Line 88 should be changed from `assertEq(currentCounterId, 0);` to `assertEq(currentCounterId, 1);`
* Line 138 should be changed from ` assertEq(turnstile.currentCounterId(), 1);` to ` assertEq(turnstile.currentCounterId(), 2);`