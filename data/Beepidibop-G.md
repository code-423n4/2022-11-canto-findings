# Gas Optimization Report

I found 3 different changes that can reduce gas usage for Turnstile.sol

1. Deprecate `Counter` and use `totalSupply` for tokenId when minting
2. Refactor `withdraw()`'s logic for updating `balances[_tokenId]`
3. Deprecate NftData, use `tokenId` to check for registration instead (This also starts tokenId at 1 instead of 0)

#3 requires structural changes. However, I have not seen `NftData` actually referenced by external contract, code, or test cases, so I included it as a serparate branch for the team to consider.

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
| register         | 656   | 157578 | 161436 | 163436 | 155     |
| transferFrom     | 42015 | 42015  | 42015  | 42015  | 1       |
| withdraw         | 760   | 4494   | 969    | 29948  | 11      |

## Gas Savings Without Structural Change:

### Compared to Original:

| Function Name | min | avg                      | median                  | max                     | # calls |
| ------------- | --- | ------------------------ | ----------------------- | ----------------------- | ------- |
| register      | 656 | 157578 -> 156224 (-1354) | 161436 -> 161209 (-227) | 163436 -> 163209 (-227) | 155     |
| withdraw      | 760 | 4494 -> 4666 (+172)      | 969                     | 29948 -> 29878 (-70)    | 11      |

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
| register         | 656   | 156224 | 161209 | 163209 | 155     |
| transferFrom     | 42015 | 42015  | 42015  | 42015  | 1       |
| withdraw         | 760   | 4666   | 969    | 29878  | 11      |

## Gas Savings With Structural Change:

### Compared to Original:

| Function Name    | min              | avg                           | median                        | max                           | # calls |
| ---------------- | ---------------- | ----------------------------- | ----------------------------- | ----------------------------- | ------- |
| assign           | 558 -> 504 (-54) | 44884 -> 23616 (-**21268**)   | 46564 -> 24184 (-**22380**)   | 46564 -> 24184 (-**22380**)   | 152     |
| currentCounterId | 374 -> 449 (+75) | 400 -> 475 (+75)              | 374 -> 449 (+75)              | 2374 -> 2449 (+75)            | 300     |
| getTokenId       | 879 -> 837 (-42) | 896 -> 854 (-42)              | 879 -> 837 (-42)              | 2671 -> 2629 (-42)            | 303     |
| isRegistered     | 661 -> 643 (-18) | 661 -> 643 (-18)              | 661 -> 643 (-18)              | 661 -> 643 (-18)              | 300     |
| register         | 656 -> 602 (-54) | 157578 -> 137290 (-**20288**) | 161436 -> 138907 (-**22529**) | 163436 -> 142907 (-**20529**) | 155     |
| withdraw         | 760              | 4494 -> 4481 (-13)            | 969                           | 29948 -> 29878 (-70)          | 11      |

| currentCounterId | 374 | 400 | 374 | 2374 | 300 |
| currentCounterId | 449 | 475 | 449 | 2449 | 300 |

| currentCounterId| 374 -> 449 (75)|400 -> 475 (75)|374 -> 449 (75)|2374 -> 2449 (75)|

### Details

| Deployment Cost | Deployment Size |
| --------------- | --------------- |
| 1730845         | 8920            |

| Function Name    | min   | avg    | median | max    | # calls |
| ---------------- | ----- | ------ | ------ | ------ | ------- |
| assign           | 504   | 23616  | 24184  | 24184  | 152     |
| balanceOf        | 629   | 642    | 629    | 2629   | 150     |
| balances         | 528   | 1028   | 528    | 2528   | 12      |
| currentCounterId | 449   | 475    | 449    | 2449   | 300     |
| distributeFees   | 513   | 12242  | 12122  | 24072  | 6       |
| getTokenId       | 837   | 854    | 837    | 2629   | 303     |
| isRegistered     | 643   | 643    | 643    | 643    | 300     |
| owner            | 2421  | 2421   | 2421   | 2421   | 1       |
| ownerOf          | 624   | 637    | 624    | 2649   | 150     |
| register         | 602   | 137290 | 138907 | 142907 | 155     |
| transferFrom     | 42015 | 42015  | 42015  | 42015  | 1       |
| withdraw         | 760   | 4481   | 969    | 29878  | 11      |
