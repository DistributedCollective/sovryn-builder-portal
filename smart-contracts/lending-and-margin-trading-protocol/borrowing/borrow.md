---
description: How to borrow funds from a lending pool.
---

# Borrow

## Borrow

borrow on the loan token contract may be used to borrow funds from the pool. It either draws more on an existing loan or opens a new position on the Sovryn Protocol. Note that the collateral has to differ from the borrowed token.&#x20;

```solidity
function borrow(bytes32 loanId, uint256 withdrawAmount, uint256 initialLoanDuration, uint256 collateralTokenSent, address collateralTokenAddress, address borrower, address receiver, bytes ) public payable nonReentrant globallyNonReentrant 
returns(uint256, uint256)
```

**Arguments**

<table><thead><tr><th width="227.33333333333334">Name</th><th width="118">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>The ID of the loan, 0 for a new loan.</td></tr><tr><td>withdrawAmount</td><td>uint256</td><td>The amount to be withdrawn (actually borrowed).</td></tr><tr><td>initialLoanDuration</td><td>uint256</td><td>The duration of the loan in seconds. If the loan is not paid back until then, it'll need to be rolled over.</td></tr><tr><td>collateralTokenSent</td><td>uint256</td><td>The amount of collateral tokens provided by the user. Needs to satisfy the minimum collateral ratio (150% for most tokens).</td></tr><tr><td>collateralTokenAddress</td><td>address</td><td>The address of the token to be used as collateral. Cannot be the loan token address.</td></tr><tr><td>borrower</td><td>address</td><td>The one paying for the collateral.</td></tr><tr><td>receiver</td><td>address</td><td>The one receiving the withdrawn amount. </td></tr><tr><td></td><td>bytes</td><td>loanId The ID of the loan, 0 for a new loan.</td></tr></tbody></table>

**Returns**

New principal and new collateral added to loan.
