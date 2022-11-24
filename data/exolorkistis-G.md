[1] FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED ``PAYABLE``

If a function modifier such as ``onlyOwner`` is used, the function will revert if a normal user tries to pay the function. Marking the function as ``payable`` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are ``CALLVALUE``(2),``DUP1``(3),``ISZERO``(3),``PUSH``2(3),``JUMPI``(10),``PUSH1``(3),``DUP1``(3),``REVERT``(0),``JUMPDEST``(1),``POP``(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

*There are 4 instances of this issue:*

```
File:  2022-11-canto/CIP-001/src/Turnstile.sol

86:  function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

107:  function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {

127-130:  function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
        public
        onlyNftOwner(_tokenId)
        returns (uint256)

148:  function distributeFees(uint256 _tokenId) public onlyOwner payable {
```

[https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L86](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L86)

[https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107)

[https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127-L130](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127-L130)

[https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148)
