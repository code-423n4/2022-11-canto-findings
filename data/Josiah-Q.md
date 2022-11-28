## ZERO ADDRESS CHECKS
Zero address checks could avoid human errors leading to non-functional calls associated with the mistakes. 

Here is 1 instance found.

[Turnstile.sol#L141](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L141)

Note that `sendValue()` from Openzeppelin will only mitigate situations where > 2300 gas is needed:

```
        Address.sendValue(_recipient, _amount);
```
## BLOATED LOG POISONING
Public and external functions with emitted events could be spammed by malicious actors to create bloated logs at the back end if checks are insufficient to stem these malicious from transpiring.

Here is 1 instance found.

[Turnstile.sol#L107-L120](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107-L120)

Note that new users could easily bypass the if check and emit undesirable events.

```
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
## GLOBAL VARIABLE CACHE
A global variable cache is unneeded since it does not entail any gas saving. 

Here are the 3 instances found.

[Turnstile.sol](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol)

```
50:        address smartContract = msg.sender;

87:        address smartContract = msg.sender;

108:        address smartContract = msg.sender;
```
## SMART CONTRACT CHECKS
It is recommended implementing either a `extcodehash` or `extcodesize` checks when the intended `msg.sender` is supposed to be a smart contract.

Here are the 2 instances found.

[Turnstile.sol#L87](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L87)

```
87:        address smartContract = msg.sender;
```
[Turnstile.sol#L108](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L108)

```
        address smartContract = msg.sender;
```
## EARLY CONDITIONAL CHECKS
All checks should be executed as early as possible in a code block. When need be, the function call could revert to avoid wastage of gas on all other code executions that come after them.

Here are the 2 instances found.

[Turnstile.sol#L89](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L89)

```
89:        if (_recipient == address(0)) revert InvalidRecipient();
```
[Turnstile.sol#L110](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L110)

```
110:        if (!_exists(_tokenId)) revert InvalidTokenId();
```