### Title
Unsafe ERC20 Operation(s)

#### Impact
Issue Information: 
ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard.

It is therefore recommended to always either use OpenZeppelin's SafeERC20 library or at least to wrap each operation in a require statement.

To circumvent ERC20's approve functions race-condition vulnerability use OpenZeppelin's SafeERC20 library's safe{Increase|Decrease}Allowance functions.

In case the vulnerability is of no danger for your implementation, provide enough documentation explaining the reasonings.

Example
🤦 Bad:

IERC20(token).transferFrom(msg.sender, address(this), amount);
🚀 Good (using OpenZeppelin's SafeERC20):

import {SafeERC20} from "openzeppelin/token/utils/SafeERC20.sol";

// ...

IERC20(token).safeTransferFrom(msg.sender, address(this), amount);
🚀 Good (using require):

bool success = IERC20(token).transferFrom(msg.sender, address(this), amount);
require(success, "ERC20 transfer failed");

#### Findings:
```
2022-11-canto/CIP-001/test/Turnstile.t.sol::279 => turnstile.transferFrom(_recipient, receiveContract, tokenId);
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::20 => super.transfer(_thief, amount - half); // a - h for rounding
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::21 => return super.transfer(recipient, half);
2022-11-canto/Canto/contracts/ERC20MaliciousDelayed.sol::22 => return super.transfer(recipient, amount);
```
#### Tools used
Manual




=================================================================================================================================


### Title
Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::13 => _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
2022-11-canto/Canto/contracts/ERC20MaliciousDelayed.sol::14 => _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::40 => _setupRole(DEFAULT_ADMIN_ROLE, _msgSender());
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::42 => _setupRole(MINTER_ROLE, _msgSender());
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::43 => _setupRole(PAUSER_ROLE, _msgSender());
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::44 => _setupRole(BURNER_ROLE, _msgSender());
```
#### Tools used
Manual