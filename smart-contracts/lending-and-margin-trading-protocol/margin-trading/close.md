---
description: How to close a margin tarde.
---

# Close

Close a position by swapping the collateral back to loan tokens, paying back the loan to the lender and withdrawing the remainder either in the loan or the collateral currency.&#x20;

```solidity
function closeWithSwap(bytes32 loanId, address receiver, uint256 swapAmount, bool returnTokenIsCollateral, bytes ) public nonpayable nonReentrant globallyNonReentrant iTokenSupplyUnchanged whenNotPaused 
returns(loanCloseAmount uint256, withdrawAmount uint256, withdrawToken address)
```

**Arguments**

<table><thead><tr><th width="160.33333333333334">Name</th><th width="138">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>The id of the loan.</td></tr><tr><td>receiver</td><td>address</td><td>The receiver of the remainder (unused collateral + profit).</td></tr><tr><td>swapAmount</td><td>uint256</td><td>Defines how much of the position should be closed and is denominated in collateral tokens. If swapAmount >= collateral, the complete position will be closed. </td></tr><tr><td>returnTokenIsCollateral</td><td>bool</td><td>Defines if the remainder should be paid out in the loan or the collateral currency. </td></tr><tr><td></td><td>bytes</td><td>loanId The id of the loan.</td></tr></tbody></table>

**Returns**

The amount of the loan which was closed, the amount of tokens sent to the receiver, the address of the tokens sent to the receiver (loan or collateral)
