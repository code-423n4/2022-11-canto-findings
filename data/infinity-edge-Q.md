1. `currentCounterId()` method is defined and tested but not used anywhere.  Can be safely removed to improve readability. [LINK](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L61)

2. `processEvent()` method does not have a nil check for `receipt` param and directly accesses the `receipt.Log` attribute. This can panic if a nil pointer is passed. [LINK](https://github.com/code-423n4/2022-11-canto/blob/main/Canto/x/csr/keeper/evm_hooks.go#L108)

3. `msg.value` is directly added to `balances[_tokenID]` without checking for overflows. While it is unlikely for tokens with 18 or less decimal values to overflow right now, over time if tokens with more decimal values are introduced or the balance gets high enough (through passage of time / lot of smart contracts contributing to the same NFT / both), it can prohibit the recipient of the NFT to withdraw their rightful award. [LINK](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151)

