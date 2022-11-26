## [1]  NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

### Consider changing the variable to be an unnamed one

> FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

        86:        function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

##

##  [2]  EVENT IS MISSING INDEXED FIELDS

>  Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it’s not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

There are 4 instances of this issue:

> FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

    28:   event Register(address smartContract, address recipient, uint256 tokenId);

    29:   event Assign(address smartContract, uint256 tokenId);

    30:   event Withdraw(uint256 tokenId, address recipient, uint256 feeAmount);

    31:   event DistributeFees(uint256 tokenId, uint256 feeAmount);

## 
  

        