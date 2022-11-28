# Gas Report for Canto (Nov-2022) by Perito Flores



### [G1] Use external instead of public for some functions



`External` modifier is cheaper than `public` so considering using external for the following functions


    function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) { |
    
    function assign(uint256 _tokenId) public onlyUnregistered returns (uint256) {
    
    function withdraw(uint256 _tokenId, address payable _recipient, uint256 _amount)
    
    function distributeFees(uint256 _tokenId) public onlyOwner payable {



### [G2] Remove zero address check for _recipient in register() 

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L89

First of all, the function `_mint` of OZ already checks that the address of recipient is different from zero so this checks is unnecessary .     In addition, as I suggested in medium report, when you use `_safeMint` this check will not be required either.    



### [G3] msg.sender is cheaper than MSTORE and MREAD

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L50-L52

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L87



Maybe you prefer to keep code as it is for readability but accessing to `msg.sender` will always be cheaper than using a local variable.

While reading `msg.sender` will cost 2 gas every MSTORE and MREAD will cost 3 gas each



### [G4]  Use unchecked blocks in these lines

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L137

https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L151



### [G5] Delete onlyOwner modifier in `distributeFees`



https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148





