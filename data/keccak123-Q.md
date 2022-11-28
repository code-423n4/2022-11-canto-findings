# QA-01

[A comment claims](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L10) that Turnstile can sit behind a proxy. This is incorrect because the contract would not function properly behind a proxy. The actions in the constructor of implementations contracts behind proxies [are ignored by the proxy](https://docs.openzeppelin.com/upgrades-plugins/1.x/proxies#the-constructor-caveat).

## Recommendation

To fix this, Turnstile should change the current OpenZeppelin imports for imports from openzeppelin-contracts-upgradeable, which are designed for implementation contracts. Ownable should be replaced [with OwnableUpgradeable](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/access/OwnableUpgradeable.sol) and ERC721Enumerable should be replaced [with ERC721EnumerableUpgradeable](https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC721/extensions/ERC721EnumerableUpgradeable.sol).

# QA-02

[This comment](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L83) and [this comment](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L49) that assume a smart contract will call these functions makes an incorrect assumption. Any EOA can call these functions too.

## Recommendation

The contract should enforce the limitation on smart contracts call these functions by preventing EOAs by calling these functions. Implement a function to allow only smart contracts with extcodesize > 0 to call these functions and call this function from functions that only should be called by smart contracts.

```solidity
    // code from https://solidity-by-example.org/hacks/contract-size/
    function isContract(address account) internal view returns (bool) {
        // This method relies on extcodesize, which returns 0 for contracts in
        // construction, since the code is only stored at the end of the
        // constructor execution.
        uint size;
        assembly {
            size := extcodesize(account)
        }
        return size > 0;
    }
```