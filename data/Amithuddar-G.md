1)X = X + Y OR X = X - Y CAN BE USED INSTEAD OF X += Y OR X -= Y

x = x + y or x = x - y costs less gas than x += y or x -= y. For example, v += 27 can be changed to v = v + 27 in the following code.   
  
File:2022-11-canto\CIP-001\src\Turnstile.sol
  151,28:         balances[_tokenId] += msg.value;

2)Multiple address mappings can be combined into a single mapping of an address to a struct

Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.
 
File:2022-11-canto\CIP-001\src\Turnstile.sol
  23,5:     mapping(address => NftData) public feeRecipient;
  26,5:     mapping(uint256 => uint256) public balances; 