X += Y costs 22 more gas than X = X + Y. This can mean a lot of gas is wasted in a function call when the computation is repeated n times (loops)
use X = X + Y instead of X += Y (same with -).

the issue is at :
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L152

/////////////////////////////////////// ********************* ///////////////////////////////////////

Add unchecked {} for subtractions where the operands cannot underflow because of a previous require()

the issue is at :
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L152

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L138


