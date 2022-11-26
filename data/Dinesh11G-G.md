### Title
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
2022-11-canto/CIP-001/test/Turnstile.t.sol::150 => vm.assume(_amount > 0 && _amount < 1000 ether);
2022-11-canto/CIP-001/test/Turnstile.t.sol::197 => vm.assume(_rewardAmount > 0 && _rewardAmount < 1000 ether);
2022-11-canto/CIP-001/test/Turnstile.t.sol::198 => vm.assume(_withdrawAmount > 0 && _withdrawAmount < 1000 ether);
2022-11-canto/CIP-001/test/Turnstile.t.sol::253 => vm.assume(_rewardAmount > 0 && _rewardAmount < 1000 ether);
2022-11-canto/CIP-001/test/Turnstile.t.sol::254 => vm.assume(_withdrawAmount > 0 && _withdrawAmount < 1000 ether);
```
#### Tools used
Manual




=================================================================================================================================


### Title
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::27 => bytes32 public constant MINTER_ROLE = keccak256("MINTER_ROLE");
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::28 => bytes32 public constant PAUSER_ROLE = keccak256("PAUSER_ROLE");
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::29 => bytes32 public constant BURNER_ROLE = keccak256("BURNER_ROLE");
2022-11-canto/Canto/contracts/caller.sol::19 => bytes4(keccak256(bytes(x.signatures[0]))),
```
#### Tools used
Manual



=================================================================================================================================



### Title
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
2022-11-canto/CIP-001/src/Turnstile.sol::5 => import "openzeppelin/token/ERC721/extensions/ERC721Enumerable.sol";
2022-11-canto/Canto/contracts/ERC20Burnable.sol::6 => import "./@openzeppelin/contracts/token/ERC20/ERC20.sol";
2022-11-canto/Canto/contracts/ERC20Burnable.sol::7 => import "./@openzeppelin/contracts/utils/Context.sol";
2022-11-canto/Canto/contracts/ERC20Burnable.sol::37 => require(currentAllowance >= amount, "ERC20: burn amount exceeds allowance");
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::5 => import "./@openzeppelin/contracts/token/ERC20/presets/ERC20PresetMinterPauser.sol";
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::12 => ERC20PresetMinterPauser("ERC20DirectBalanceManipulation", "ERC20DirectBalanceManipulation") {
2022-11-canto/Canto/contracts/ERC20MaliciousDelayed.sol::5 => import "./@openzeppelin/contracts/token/ERC20/presets/ERC20PresetMinterPauser.sol";
2022-11-canto/Canto/contracts/ERC20MaliciousDelayed.sol::13 => ERC20PresetMinterPauser("ERC20MaliciousDelayed", "ERC20MALICIOUSDELAYED") {
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::6 => import "./@openzeppelin/contracts/token/ERC20/ERC20.sol";
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::7 => import "./@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::8 => import "./@openzeppelin/contracts/token/ERC20/extensions/ERC20Pausable.sol";
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::9 => import "./@openzeppelin/contracts/access/AccessControlEnumerable.sol";
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::10 => import "./@openzeppelin/contracts/utils/Context.sol";
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::72 => require(hasRole(MINTER_ROLE, _msgSender()), "ERC20MinterBurnerDecimals: must have minter role to mint");
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::86 => require(hasRole(BURNER_ROLE, _msgSender()), "ERC20MinterBurnerDecimals: must have burner role to burn");
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::100 => require(hasRole(PAUSER_ROLE, _msgSender()), "ERC20MinterBurnerDecimals: must have pauser role to pause");
2022-11-canto/Canto/contracts/ERC20MinterBurnerDecimals.sol::114 => require(hasRole(PAUSER_ROLE, _msgSender()), "ERC20MinterBurnerDecimals: must have pauser role to unpause");
2022-11-canto/Canto/contracts/turnstile.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
2022-11-canto/Canto/contracts/turnstile.sol::5 => import "@openzeppelin/contracts/token/ERC721/extensions/ERC721Enumerable.sol";
2022-11-canto/Canto/contracts/turnstile.sol::6 => import "@openzeppelin/contracts/utils/Counters.sol";
```
#### Tools used
Manual






=================================================================================================================================



### Title
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
2022-11-canto/CIP-001/test/Turnstile.t.sol::35 => address smartContract = address(uint160(i*200));
2022-11-canto/CIP-001/test/Turnstile.t.sol::36 => address smartContract2 = address(uint160(i*200000));
2022-11-canto/CIP-001/test/Turnstile.t.sol::184 => assertEq(turnstile.balances(tokenId), _amount * 2);
2022-11-canto/CIP-001/test/Turnstile.t.sol::185 => assertEq(address(turnstile).balance, _amount * 2);
2022-11-canto/Canto/contracts/ERC20DirectBalanceManipulation.sol::18 => uint256 half = amount / 2;
```
#### Tools used
Manual