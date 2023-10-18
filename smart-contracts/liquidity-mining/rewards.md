---
description: It is possible to claim rewards for specific pools or all of them at once.
---

# Rewards

## For a specific pool token

This function claims the rewards for a specific pool token.

```solidity
function claimReward(address _poolToken, address _user) external nonpayable
```

**Arguments**

<table><thead><tr><th width="166.33333333333334">Name</th><th width="126">Type</th><th>Description</th></tr></thead><tbody><tr><td>_poolToken</td><td>address</td><td>the address of the pool token</td></tr><tr><td>_user</td><td>address</td><td>the address of user to claim reward from. Only the wrapper contract may pass a non-zero value. For all other cases the msg.sender will be used.</td></tr></tbody></table>



## For all pool tokens

This function claims the rewards for all pool tokens that the user has deposited.

```solidity
function claimRewardFromAllPools(address _user) external nonpayable
```

**Arguments**

<table><thead><tr><th width="140.33333333333334">Name</th><th width="154">Type</th><th>Description</th></tr></thead><tbody><tr><td>_user</td><td>address</td><td>the address of user to claim reward from. Only the wrapper contract may pass a non-zero value. For all other cases the msg.sender will be used.</td></tr></tbody></table>
