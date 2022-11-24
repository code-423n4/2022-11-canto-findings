## [1]  NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

### Consider changing the variable to be an unnamed one

> FILE :  2022-11-canto/CIP-001/src/Turnstile.sol

        86:        function register(address _recipient) public onlyUnregistered returns (uint256 tokenId) {

        