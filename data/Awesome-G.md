## 1. Functions With Access Control Cheaper if Payable

A function with access control marked as payable will be cheaper for legitimate callers: the compiler removes checks for `msg.value`, saving approximately `20` gas per function call.

There are 3 instances of this issue:

[Line 86](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L86), [Line 107](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L107), [Line 127-130](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L127-L130)

```
File: src/Turnstile.sol

86:    function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

107:   function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {

127:    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
128        public
129        onlyNftOwner(_tokenId)
130        returns (uint256)
```

## 2. Remove Shorthand Addition/Subtraction Assignment

Instead of using the shorthand of addition/subtraction assignment operators (`+=`, `-=`)
it costs less to remove the shorthand (`x += y` same as `x = x+y`) saves ~22 gas

There is 1 instance of this issue:

[Line 151](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151)

```
File: src/Turnstile.sol
Line 151:    balances[_tokenId] += msg.value;
```

Could be rewritten to as

```
Line 151:    balances[_tokenId] = balances[_tokenId] + msg.value;
```
