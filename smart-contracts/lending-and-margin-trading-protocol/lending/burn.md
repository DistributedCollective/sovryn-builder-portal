---
description: >-
  Users may burn their iTokens to retrieve the underlying assets. One cannot
  withdraw more assets than are currently available in the pool.
---

# Burn

## Burn to ERC20

Call burn to withdraw the underlying assets from the lending pool. If the iTokens reside on the Liquidity Mining Contract, they can be retrieved in the same transaction.&#x20;

```solidity
function burn(address receiver, uint256 burnAmount, bool useLM) external nonpayable nonReentrant globallyNonReentrant 
returns(uint256 redeemed)
```

**Arguments**

<table><thead><tr><th width="150.33333333333334">Name</th><th width="126">Type</th><th>Description</th></tr></thead><tbody><tr><td>receiver</td><td>address</td><td>the receiver of the underlying tokens. note: potential LM rewards are always sent to the msg.sender</td></tr><tr><td>burnAmount</td><td>uint256</td><td>the amount of pool tokens to redeem.</td></tr><tr><td>useLM</td><td>bool</td><td>if true -> retrieve the pool tokens into the Liquidity Mining contract</td></tr></tbody></table>

**Returns**

&#x20;The amount of tokens redeemed.&#x20;



## Burn to RBTC

```solidity
function burnToBTC(address receiver, uint256 burnAmount, bool useLM) external nonpayable nonReentrant globallyNonReentrant 
returns(uint256 loanAmountPaid)
```

**Arguments**

<table><thead><tr><th width="153.33333333333334">Name</th><th width="107">Type</th><th>Description</th></tr></thead><tbody><tr><td>receiver</td><td>address</td><td>the receiver of the underlying tokens. note: potential LM rewards are always sent to the msg.sender</td></tr><tr><td>burnAmount</td><td>uint256</td><td>the amount of pool tokens to redeem.</td></tr><tr><td>useLM</td><td>bool</td><td>if true -> retrieve the pool tokens into the Liquidity Mining contract</td></tr></tbody></table>
