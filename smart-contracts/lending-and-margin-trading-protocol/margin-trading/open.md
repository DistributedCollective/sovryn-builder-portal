---
description: How to open a margin trade.
---

# Open

## marginTrade

marginTrade opens a leveraged position on the Sovryn Protocol. It can be used to open a new position or modify an existing one.&#x20;

```solidity
function marginTrade(bytes32 loanId, uint256 leverageAmount, uint256 loanTokenSent, uint256 collateralTokenSent, address collateralTokenAddress, address trader, uint256 minEntryPrice, bytes loanDataBytes) public payable nonReentrant globallyNonReentrant 
returns(uint256, uint256)
```

**Arguments**

<table><thead><tr><th width="226.33333333333334">Name</th><th width="141">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>The ID of the loan, 0 for a new loan.</td></tr><tr><td>leverageAmount</td><td>uint256</td><td>The multiple of exposure: 2x ... 5x. The leverage with 18 decimals.</td></tr><tr><td>loanTokenSent</td><td>uint256</td><td>The number of loan tokens provided by the user.</td></tr><tr><td>collateralTokenSent</td><td>uint256</td><td>The amount of collateral tokens provided by the user.</td></tr><tr><td>collateralTokenAddress</td><td>address</td><td>The token address of collateral.</td></tr><tr><td>trader</td><td>address</td><td>The account that performs this trade.</td></tr><tr><td>minEntryPrice</td><td>uint256</td><td>Value of loan token in collateral.</td></tr><tr><td>loanDataBytes</td><td>bytes</td><td>Additional loan data (not in use for token swaps). *</td></tr></tbody></table>

**Returns**

New principal and new collateral added to the position
