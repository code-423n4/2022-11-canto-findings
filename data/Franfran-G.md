### `totalAssets()` caching
Store the output of `totalAssets()` into a variable when called later for gas savings and readability.

https://github.com/code-423n4/2022-11-redactedcartel/blob/main/src/vaults/AutoPxGmx.sol#L288-L290

Suggestion:

```solidity
uint256 totalAssets = totalAssets();

if ((totalAssets - assetsBeforeClaim) != 0) {
    totalFee =
        ((totalAssets - assetsBeforeClaim) *
            platformFee) /
            FEE_DENOMINATOR;
    incentive = optOutIncentive
        ? 0
        : (totalFee * compoundIncentive) / FEE_DENOMINATOR;

    if (incentive != 0) asset.safeTransfer(msg.sender, incentive);

    asset.safeTransfer(owner, totalFee - incentive);
}
```

