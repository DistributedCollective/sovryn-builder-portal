---
description: >-
  Deposit tokens to the liquidity mining contract in order to start accumulating
  rewards.
---

# Deposit

Call this function to deposit pool tokens.

```solidity
function deposit(address _poolToken, uint256 _amount, address _user) external nonpayable
```

**Arguments**

<table><thead><tr><th width="146.33333333333334">Name</th><th width="127">Type</th><th>Description</th></tr></thead><tbody><tr><td>_poolToken</td><td>address</td><td>the address of the pool token to deposit</td></tr><tr><td>_amount</td><td>uint256</td><td>the amount of the pool tokens to deposit</td></tr><tr><td>_user</td><td>address</td><td>the address of the user, tokens will be deposited to it or to msg.sender if 0</td></tr></tbody></table>
