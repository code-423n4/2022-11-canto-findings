| | issue |
| ----------- | ----------- |
| 1 | [Stack variable used as a cheaper cache for a state variable is only used once](#1-stack-variable-used-as-a-cheaper-cache-for-a-state-variable-is-only-used-once) |
| 2 | [Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement](#2-add-unchecked--for-subtractions-where-the-operands-cannot-underflow-because-of-a-previous-require-or-if-statement) |
| 3 | [`<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables](#3-x--y-costs-more-gas-than-x--x--y-for-state-variables) |
| 4 | [using `bool` for storage incurs overhead](#4-using-bool-for-storage-incurs-overhead) |
| 5 | [public functions not called by the contract should be declared external instead](#5-public-functions-not-called-by-the-contract-should-be-declared-external-instead) |
| 6 | [make stack variables when needed](#6-make-stack-variables-when-needed) |
| 7 | [cache msg.sender in a stack variable](#7-cache-msgsender-in-a-stack-variable) |
| 8 | [using functions will use extra gas for jumps which can be saved otherwise](#8-using-functions-will-use-extra-gas-for-jumps-which-can-be-saved-otherwise) |

## 1. Stack variable used as a cheaper cache for a state variable is only used once

If the variable is only accessed once, it’s cheaper to use the state variable directly that one time, and save the 3 gas the extra stack assignment would spend

- [Turnstile.sol#L50](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L50)


## 2. Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if` statement

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
this will stop the check for overflow and underflow so it will save gas

- [Turnstile.sol#L137](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L137)


## 3. `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves gas

- [Turnstile.sol#L151](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151)


## 4. using `bool` for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past

- [Turnstile.sol#L17](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L17)


## 5. public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

- [Turnstile.sol#L86](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L86)
- [Turnstile.sol#L107](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L107)
- [Turnstile.sol#L127](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L127)
- [Turnstile.sol#L148](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L148)


## 6. make stack variables when needed

make stack variables when they are needed instead of at the start of the function.

make `smartContract` after the if statement because if the check fails we wont need it
- [Turnstile.sol#L108](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L108)
- [Turnstile.sol#L87](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L87)

## 7. cache msg.sender in a stack variable

cache it before line 149 for possible gas save
- [Turnstile.sol#L149](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L149)

## 8. using functions will use extra gas for jumps which can be saved otherwise

using inlined form here can save gas that is used for calling of functions. these are really simple functions and can be inlined without ruining readability and to save gas. (until a way is implemented to restrict changing the _value we can simply change the state var instead of using functions for it).

use `unchecked {++_tokenIdTracker.counter._value;}` instead of `_tokenIdTracker.increment()`. the increment function doesnt use ++x and uses x += 1 instead which will waste gas itself.
- [Turnstile.sol#L93](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L93)

use `_tokenIdTracker.counter._value` instead of `_tokenIdTracker.current()`
- [Turnstile.sol#L62](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L62)
- [Turnstile.sol#L91](https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L91)
