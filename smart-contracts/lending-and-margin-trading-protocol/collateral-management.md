---
description: The user may add or remove collateral to / from an open position.
---

# Collateral Management

## Adding collateral

Increase the margin of a position by depositing additional collateral.&#x20;

```solidity
function depositCollateral(bytes32 loanId, uint256 depositAmount) external payable nonReentrant whenNotPaused 
```

**Arguments**

<table><thead><tr><th width="190.33333333333334">Name</th><th width="147">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>A unique ID representing the loan.</td></tr><tr><td>depositAmount</td><td>uint256</td><td>The amount to be deposited in collateral tokens.</td></tr></tbody></table>



## Removing collateral

Withdraw from the collateral. This reduces the margin of a position. Note that it is not possible to reduce the margin below the maintenance margin. If attempted, as much collateral as possible will be removed, leaving the position at the maintenance margin level.

```solidity
function withdrawCollateral(bytes32 loanId, address receiver, uint256 withdrawAmount) external nonpayable nonReentrant whenNotPaused 
returns(actualWithdrawAmount uint256)
```

**Arguments**

<table><thead><tr><th width="187.33333333333334">Name</th><th width="116">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>A unique ID representing the loan.</td></tr><tr><td>receiver</td><td>address</td><td>The account getting the withdrawal.</td></tr><tr><td>withdrawAmount</td><td>uint256</td><td>The amount to be withdrawn in collateral tokens. </td></tr></tbody></table>

**Returns**

The amount withdrawn&#x20;
