QA1: 
https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L86
``msg.sender`` is assumed to be a contract. Such assumption can be eliminated by the function ``isContract()`` proposed by openZepplin: 
https://docs.openzeppelin.com/contracts/2.x/api/utils

QA2. 

payable(receiver).call{value: msg.value}("");
