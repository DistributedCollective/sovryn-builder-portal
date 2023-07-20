---
description: How to close a position on the Sovryn Protocol.
---

# Repay

## Close with Deposit

closeWithDeposit closes a loan partially or completely by depositing loan tokens to the Sovryn Protocol. Note: It is also possible to close with a swap, see [Margin Trading](../margin-trading/).

```solidity
function closeWithDeposit(bytes32 loanId, address receiver, uint256 depositAmount) public payable nonReentrant globallyNonReentrant iTokenSupplyUnchanged whenNotPaused 
returns(loanCloseAmount uint256, withdrawAmount uint256, withdrawToken address)
```

**Arguments**

<table><thead><tr><th width="186.33333333333334">Name</th><th width="130">Type</th><th>Description</th></tr></thead><tbody><tr><td>loanId</td><td>bytes32</td><td>The id of the loan.</td></tr><tr><td>receiver</td><td>address</td><td>The receiver of the remainder.</td></tr><tr><td>depositAmount</td><td>uint256</td><td>Defines how much of the position should be closed. It is denominated in loan tokens. If depositAmount > principal, the complete loan will be closed.</td></tr></tbody></table>

**Returns**

the amount of the loan which was closed, the amount of the collateral token which was withdrawn to the receiver, the collateral token address of the loan
