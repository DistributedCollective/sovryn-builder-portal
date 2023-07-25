---
description: How to withdraw pool tokens from the Liquidity Mining contract.
---

# Withdraw

Call this function to withdraw pool tokens and the accumulated reward tokens.

```solidity
function withdraw(address _poolToken, uint256 _amount, address _user) external nonpayable
```

**Arguments**

<table><thead><tr><th width="158.33333333333334">Name</th><th width="156">Type</th><th>Description</th></tr></thead><tbody><tr><td>_poolToken</td><td>address</td><td>the address of the pool token to withdraw</td></tr><tr><td>_amount</td><td>uint256</td><td>the amount of the pool tokens to withdraw</td></tr><tr><td>_user</td><td>address</td><td>the user address will be used to process a withdrawal. Only the wrapper contract may pass a non-zero value. For all other cases the msg.sender will be used.</td></tr></tbody></table>
