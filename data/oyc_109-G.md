## [G-01] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
Turnstile.sol::86 => function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {
Turnstile.sol::107 => function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
```

## [G-02] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
Turnstile.sol::57 => constructor() ERC721("Turnstile", "Turnstile") {}
```

## [G-03] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
Turnstile.sol::151 => balances[_tokenId] += msg.value;
```

## [G-04] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
Turnstile.sol::107 => function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
Turnstile.sol::148 => function distributeFees(uint256 _tokenId) public onlyOwner payable {
```