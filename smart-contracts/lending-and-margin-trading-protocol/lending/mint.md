---
description: >-
  Users may provide funds to the lending pool in exchange for iTokens, which
  represent shares in the pool.
---

# Mint

## Mint with ERC20

In order to provide funds to the pool, call `mint` on the respective iToken contract. This will take your deposit and give you iTokens in return. The function retrieves the tokens from the message sender, so make sure to first approve the loan token contract to access your funds.&#x20;

```solidity
function mint(address receiver, uint256 depositAmount, bool useLM) external nonpayable nonReentrant globallyNonReentrant 
returns(uint256 minted)
```

**Arguments**

<table><thead><tr><th width="173.33333333333334">Name</th><th width="118">Type</th><th>Description</th></tr></thead><tbody><tr><td>receiver</td><td>address</td><td>the receiver of the tokens</td></tr><tr><td>depositAmount</td><td>uint256</td><td>the amount of underlying tokens provided on the loan. (Not the number of loan tokens to mint).</td></tr><tr><td>useLM</td><td>bool</td><td>if true -> deposit the pool tokens into the Liquidity Mining contract</td></tr></tbody></table>

**Returns**

The amount of loan tokens minted



## Mint with RBTC

In order to provide funds to the RBTC lending pool, call `mintWithBTC` on the iWRBTC contract. This will take your deposit and give you iWRBTC in return.&#x20;

```solidity
function mintWithBTC(address receiver, bool useLM) external payable nonReentrant globallyNonReentrant 
returns(mintAmount uint256)
```

**Arguments**

| Name     | Type    | Description                                                           |
| -------- | ------- | --------------------------------------------------------------------- |
| receiver | address | the receiver of the tokens                                            |
| useLM    | bool    | if true -> deposit the pool tokens into the Liquidity Mining contract |
