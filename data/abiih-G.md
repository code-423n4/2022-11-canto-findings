# Report

---

## Gas Optimizations

---

|       |                          Issue                          | Instances | Total Gas Saved on Deployment[Estimated] |
| ----- | :-----------------------------------------------------: | --------: | ---------------------------------------: |
| GAS-1 | Use global variable msg.sender directly without caching |         3 |                                     4760 |
| GAS-2 | Strict inequalities are expensive than Non-strict ones  |         1 |                                      216 |

---

Total: 4 instances over 2 issues with estimated 4976 gas saved on deployment.

#### [GAS-1] Use global variable msg.sender directly without caching

It is cheaper to use the global variable msg.sender directly rather
than caching it. We can save about 4760 in gas at deployment and also save gas on every call.

```
File: /CIP-001/src/Turnstile.sol
50:     address smartContract = msg.sender;
87:     address smartContract = msg.sender;
108:     address smartContract = msg.sender;

```

- https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol

#### Mitigation

Use the msg.sender directly

#### [GAS-2] Strict inequalities are more expensive than Non-strict ones

Non-strict inequalities (>=) are cheaper than strict ones (>). This is so as strict inequalities add a check of nonequality which costs around 3 gas.

```
File: /CIP-001/src/Turnstile.sol
135:    if (_amount > earnedFees) _amount = earnedFees;

```

- https://github.com/code-423n4/2022-11-canto/blob/main/CIP-001/src/Turnstile.sol

#### Mitigation

```
File: /CIP-001/src/Turnstile.sol
135:    if (_amount >=  earnedFees) _amount = earnedFees;

```
