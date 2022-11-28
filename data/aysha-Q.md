Unindexed event parameters: Parameters of certain events are expected to be indexed (e.g. ERC20 Transfer/Approval events) so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#unindexed-erc20-event-oarameters )

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L28-L31
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L28-L31
==========================================================

Missing zero address validation: Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever. (see here: https://github.com/crytic/slither/wiki/Detector-Documentation#missing-zero-address-validation )

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/Canto/contracts/turnstile.sol#L128
==========================================================
