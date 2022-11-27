## Private/Internal Function Embedded Modifier Reduces Contract Size
Consider having the logic of a modifier embedded through a private (doesn't matter whether or not the contract entails any child contracts since the private visibility saves even more gas on function calls than the internal visibility) function to reduce contract size if need be.

There are two instances entailed:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L41-L55

```
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
```
They may be refactored as follows:

```
    function _onlyNftOwner(uint256 _tokenId) private view {
        if (ownerOf(_tokenId) != msg.sender) revert NotAnOwner();
    }

    modifier onlyNftOwner(uint256 _tokenId) {
        _onlyNftOwner(_tokenId);
        _;
    }

    function _onlyUnregistered private view {
        address smartContract = msg.sender;

        if (isRegistered(smartContract)) revert AlreadyRegistered();
    }

    modifier onlyUnregistered() {
        _onlyUnregistered();
        _;
    }
```
## Function Order Affects Gas Consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the Optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
  solidity: {
    version: "0.8.17",
    settings: {
      optimizer: {
        enabled: true,
        runs: 1000,
      },
    },
  },
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:
```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Using Booleans Costs More Storage Overhead
According to Openzeppelin's `ReentrancyGuard.sol`:

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L35

    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.

    // The values being non-zero value makes deployment a bit more expensive,
    // but in exchange the refund on every call to nonReentrant will be lower in
    // amount. Since refunds are capped to a percentage of the total
    // transaction's gas, it is best to keep them low in cases like this one, to
    // increase the likelihood of the full refund coming into effect.

Consider using uint256(1) and uint256(2) for true/false to avoid:

1. repeated Gwarmaccess  that would cost 100 gas each, and
2. Gsset that would cost 20000 gas when changing from ‘false’ to ‘true’, as well as after having been ‘true’ in the past.

Here is the instance entailed:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L15-L18

```
    struct NftData {
        uint256 tokenId;
        bool registered;
    }
```
## Use of Named Returns for Local Variables Saves Gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

As an example, the following instance of code block can refactored as follows:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107-L120

```
    function assign(uint256 _tokenId) public onlyUnregistered returns (uint256 tokenId) {
        address smartContract = msg.sender;

        if (!_exists(_tokenId)) revert InvalidTokenId();

        emit Assign(smartContract, _tokenId);

        feeRecipient[smartContract] = NftData({
            tokenId: _tokenId,
            registered: true
        });

        tokenId = _tokenId;
    }
```
All other instances entailed:

[File: Turnstile.sol](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)

```
61:    function currentCounterId() external view returns (uint256) {

68:    function getTokenId(address _smartContract) external view returns (uint256) {

77:    function isRegistered(address _smartContract) public view returns (bool) {

130:        returns (uint256)
```
## += and -= Costs More Gas
`+=` generally costs 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

Here is the instance entailed:

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151

```
        balances[_tokenId] += msg.value;
```
It may be refactored as follows:

```
        balances[_tokenId] = balances[_tokenId] + msg.value;
```
## Payable Access Control Functions Costs Less Gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

Here are the instances entailed:

[File: Turnstile.sol](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)

```
86:    function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

107:    function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {

129:        onlyNftOwner(_tokenId)
```

