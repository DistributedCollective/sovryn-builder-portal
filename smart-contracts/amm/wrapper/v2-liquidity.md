---
description: >-
  Providing liquidity with the wrapper has the benefit that RBTC is
  automatically wrapped to WRBTC and the pool tokens are automatically deposited
  at the liquidity mining contract.
---

# V2 liquidity

## Adding liquidity

addLiquiditytoV2 accepts RBTC, wraps RBTC to WRBTC, adds liquidity to the converter contract and transfers the pool tokens to the liquidity mining contract.&#x20;

```solidity
function addLiquidityToV2(
        address _liquidityPoolConverterAddress, 
        address _reserveAddress,
        uint256 _amount, 
        uint256 _minReturn)  
returns(uint256) 
```

**Arguments**

<table><thead><tr><th width="226.33333333333334">Name</th><th width="134">Type</th><th>Description</th></tr></thead><tbody><tr><td>_liquidityPoolConverterAddress</td><td>address</td><td>address of LiquidityPoolConverter contract</td></tr><tr><td>_reserveAddress</td><td>address</td><td>address of the reserve token to add liquidity to</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of liquidity to add</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return-amount of pool tokens </td></tr></tbody></table>

**Returns**

The amount of pool tokens minted.



## Removing liquidity

removeLiquidityFromV2 withdraws the pool tokens from the liquidity mining contract, removes the liquidity from the converter contract , unwraps WRBTC to RBTC and sends those to the user.&#x20;

```solidity
function removeLiquidityFromV2(
    address _liquidityPoolConverterAddress, 
    address _reserveAddress,
    uint256 _amount, 
    uint256 _minReturn) 
returns(uint256) 
```

**Arguments**

<table><thead><tr><th width="225.33333333333334">Name</th><th width="135">Type</th><th>Description</th></tr></thead><tbody><tr><td>_liquidityPoolConverterAddress</td><td>address</td><td>address of the LiquidityPoolConverter contract</td></tr><tr><td>_poolToken</td><td>address</td><td>address of the pool token</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of pool tokens to burn</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return-amount of reserve tokens </td></tr></tbody></table>

**Returns**

The amount of liquidity removed.
