Gas Optimization 

[1] x += y cost more gas. You can use x = x + y instead.
Although it is easier to change the value of a variable by using += or -=. It cost more gas than the written out version x = x + y and x = x - y.

There is 1 instances of this issue:

Turnstile.sol
https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol#L151
ln 151