# Gas Optimization Report

I found 3 different changes that can reduce gas usage for Turnstile.sol

1. Deprecate `Counter` and use `totalSupply` for tokenId when minting
2. Refactor `withdraw()`'s logic for updating `balances[_tokenId]`
3. Deprecate NftData, use `tokenId` to check for registration instead (With a special case added for `tokenId == 0`)

#3 requires structural changes. However, I have not seen `NftData` actually been referenced by external contract, code, or test cases, so I included it as a serparate branch for the team to consider.

The two branches can be found here:

- https://github.com/code-423n4/2022-11-canto/compare/main...beepidibop:2022-11-canto:dev/gas-savings
- https://github.com/code-423n4/2022-11-canto/compare/main...beepidibop:2022-11-canto:dev/gas-savings-with-struct-change

Here are the gas reports:

## Original:

| Deployment Cost | Deployment Size |
| --------------- | --------------- |
| 1729845         | 8915            |

| Function Name    | min   | avg    | median | max    | # calls |
| ---------------- | ----- | ------ | ------ | ------ | ------- |
| assign           | 558   | 44884  | 46564  | 46564  | 152     |
| balanceOf        | 629   | 642    | 629    | 2629   | 150     |
| balances         | 528   | 1028   | 528    | 2528   | 12      |
| currentCounterId | 374   | 400    | 374    | 2374   | 300     |
| distributeFees   | 513   | 12242  | 12122  | 24072  | 6       |
| getTokenId       | 879   | 896    | 879    | 2671   | 303     |
| isRegistered     | 661   | 661    | 661    | 661    | 300     |
| owner            | 2421  | 2421   | 2421   | 2421   | 1       |
| ownerOf          | 624   | 637    | 624    | 2649   | 150     |
| register         | 656   | 157540 | 161436 | 161436 | 154     |
| transferFrom     | 26095 | 26095  | 26095  | 26095  | 1       |
| withdraw         | 760   | 4494   | 969    | 29948  | 11      |

## Gas Savings Without Structural Change:

### Compared to Original:

| Function Name | min | avg                      | median                  | max                     | # calls |
| ------------- | --- | ------------------------ | ----------------------- | ----------------------- | ------- |
| register      | 656 | 157540 -> 156178 (-1362) | 161436 -> 161209 (-227) | 161436 -> 161209 (-227) | 154     |
| withdraw      | 760 | 4494 -> 4481 (-13)       | 969                     | 29948 -> 29878 (-70)    | 11      |

### Details

| Deployment Cost | Deployment Size |
| --------------- | --------------- |
| 1729845         | 8915            |

| Function Name    | min   | avg    | median | max    | # calls |
| ---------------- | ----- | ------ | ------ | ------ | ------- |
| assign           | 558   | 44884  | 46564  | 46564  | 152     |
| balanceOf        | 629   | 642    | 629    | 2629   | 150     |
| balances         | 528   | 1028   | 528    | 2528   | 12      |
| currentCounterId | 374   | 400    | 374    | 2374   | 300     |
| distributeFees   | 513   | 12242  | 12122  | 24072  | 6       |
| getTokenId       | 879   | 896    | 879    | 2671   | 303     |
| isRegistered     | 661   | 661    | 661    | 661    | 300     |
| owner            | 2421  | 2421   | 2421   | 2421   | 1       |
| ownerOf          | 624   | 637    | 624    | 2649   | 150     |
| register         | 656   | 156178 | 161209 | 161209 | 154     |
| transferFrom     | 26095 | 26095  | 26095  | 26095  | 1       |
| withdraw         | 760   | 4481   | 969    | 29878  | 11      |

## Gas Savings With Structural Change:

### Compared to Original:

| Function Name | min               | avg                           | median                        | max                           | # calls |
| ------------- | ----------------- | ----------------------------- | ----------------------------- | ----------------------------- | ------- |
| assign        | 558 -> 761 (+203) | 44884 -> 25882 (**-19002**)   | 46564 -> 26469 (**-20095**)   | 46564 -> 26780 (**-19784**)   | 152     |
| getTokenId    | 879 -> 896 (+17)  | 896 -> 942 (+46)              | 879 -> 896 (+17)              | 2671 -> 4874 (+2203)          | 303     |
| isRegistered  | 661 -> 678 (+17)  | 661 -> 685 (+24)              | 661 -> 678 (+17)              | 661 -> 864 (+203)             | 300     |
| register      | 656 -> 859 (+203) | 157540 -> 137413 (**-20127**) | 161436 -> 141117 (**-20319**) | 161436 -> 141117 (**-20319**) | 154     |
| withdraw      | 760               | 4494 -> 4481 (-13)            | 969                           | 29948 -> 29878 (-70)          | 11      |

### Details

| Deployment Cost | Deployment Size |
| --------------- | --------------- |
| 1730845         | 8920            |

| Function Name    | min   | avg    | median | max    | # calls |
| ---------------- | ----- | ------ | ------ | ------ | ------- |
| assign           | 761   | 25882  | 26469  | 26780  | 152     |
| balanceOf        | 629   | 642    | 629    | 2629   | 150     |
| balances         | 528   | 1028   | 528    | 2528   | 12      |
| currentCounterId | 374   | 400    | 374    | 2374   | 300     |
| distributeFees   | 513   | 12242  | 12122  | 24072  | 6       |
| getTokenId       | 896   | 942    | 896    | 4874   | 303     |
| isRegistered     | 678   | 685    | 678    | 864    | 300     |
| owner            | 2421  | 2421   | 2421   | 2421   | 1       |
| ownerOf          | 624   | 637    | 624    | 2649   | 150     |
| register         | 859   | 137413 | 141117 | 141117 | 154     |
| transferFrom     | 26095 | 26095  | 26095  | 26095  | 1       |
| withdraw         | 760   | 4481   | 969    | 29878  | 11      |
