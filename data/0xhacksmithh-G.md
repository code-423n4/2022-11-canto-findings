### [GAS-01] Functions that could be external instead of public

*instances(4)*

```solidity
File::   contracts/turnstile.sol

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L86
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127-L130
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148
```

### [GAS-02] <x> += <y> is more gas costly than <x> = <x> + <y>

*instances(1)*

```solidity
File::   contracts/turnstile.sol

https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151
```