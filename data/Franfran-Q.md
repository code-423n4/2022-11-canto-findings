### Unsafe casting of timestamp to `uint32`

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L293
https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/PirexRewards.sol#L319

A casting of the timestamp to an uint32 means that this function cast will revert in about 80 years, which may be an undesired behaviour. And this would as a consequence, freeze all tokens inheriting from `PxERC20`. So the `pxGmx` and `pxGlp`.

