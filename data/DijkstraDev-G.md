- Register function:
    - Replace the use of the `Counter` library by the use of a private variable of type uint256 [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L91).
        - register:
            - 157540 -> 157528 (-12 gas units).
        - currentCounterId:
            - 400 -> 362 (-38 gas units | 9.5 %)
        - deployment:
            - 1729845 -> 1725638 (-4162 gas units | ~ 0.24%)

- Replace variable `smartContract` with direct use of msg.value [L50](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L50), [L69](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L69), [L95](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L95), [L97](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L97), [L112](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L112) and [L114](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L114)
    - deployment:
        - 1725638 -> 1717231 (-8407 gas units | ~ 0.49%)
    - register:
        - 157528 -> 157484 (- 44 gas units | ~ 0.03%)
    - assign:
        - 44884 -> 44817 (-67 gas units | ~ 0.15%)
    
- Assign:
    - Replace the use of `_exist(_tokenId)` with `if(_tokenId >= _tokenIdTracker)`. The use that is given is only to know if the token is valid. _It would fail in the event that the NFT was burned, although it is unlikely that the owner would do so of their own free will and in the event that it does, it would not cause any serious problems. The use of `_exist(_tokenID)` in the [`distributeFees`](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L148) function would be more useful_ [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L110)
        - deployment:
            - 1717231 -> 1713424 ( -3807 gas units | ~ 0.22%)
        - assign:
            - 44817 -> 44714 (-103 gas units | ~ 0.23%)

- Withdraw:
    - Swap the order of the `If` statements. This way if the `earnedFee` variable is _zero_, any value of `_amount` will be updated to _zero_. With this, we can simplify the now second `If` statement, which would look like this: `if (_amount == 0) revert NothingToWithdraw()` [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L134)
        - 5173 -> 4673 (-500 gas units | ~ 9.67%)
        - deployment:
            - 1713424 -> 1711824 (-1600 gas units | ~ 0.09%)
    - Replace `Address.sendValue(...)` with `_recipient.call{value: _amount}("")`. Balance will always be limited to `balance[_tokenId]` [see code](https://github.com/code-423n4/2022-11-canto/blob/2733fdd1bee73a6871c6243f92a007a0b80e4c61/CIP-001/src/Turnstile.sol#L141).
        - 4673 -> 4484 (-189 gas units | ~ 4.05%)
        - deployment:
            - 1711824 -> 1672982 (-38842 gas units | ~ 2.27%)

Total (applying all the above points):
- register -> -56 gas units | ~ 0.04%
- currentCounterId -> -38 gas units | 9.5 %
- assign -> -170 gas units | ~ 0.38%
- withdraw -> -689 gas units | ~ 13.32%
- deployment -> -56863 gas units | ~ 3.29%