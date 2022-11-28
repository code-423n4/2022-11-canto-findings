

---

## Summary

- G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables | 1 instance
- G-02 Empty blocks should be removed or emit something | 1 instance
- G-03 Public functions to external | 3 instances

Total: 5 instances in 3 issues

---

## G-01 <x> += <y> costs more gas than <x> = <x> + <y> for state variables

Instance (1):

Turnstile.sol
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151
 

## G-02 Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 
If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. 
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

Instance (1):

Turnstile.sol
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L57


## G-03 Public functions to external

Public functions not called by the contract should be declared external instead. 
Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 
https://docs.soliditylang.org/en/latest/contracts.html#function-overriding

Instance (3):

Turnstile.sol
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L128
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148



