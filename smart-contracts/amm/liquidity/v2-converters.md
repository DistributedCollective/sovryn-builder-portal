---
description: >-
  V2 Converters allow for one-sided liquidity provision and removal. There is
  one LP token per reserve asset.
---

# V2 Converters

## Adding Liquidity

addLiquidity increases the pool's liquidity and mints new shares in the pool to the caller.

```solidity
function addLiquidity(IERC20Token _reserveToken, uint256 _amount, uint256 _minReturn) public payable protected active validReserve greaterThanZero greaterThanZero 
returns(uint256)
```

**Arguments**

<table><thead><tr><th width="187.33333333333334">Name</th><th width="162">Type</th><th>Description</th></tr></thead><tbody><tr><td>_reserveToken</td><td>IERC20Token</td><td>address of the reserve token to add liquidity to</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of liquidity to add</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return-amount of pool tokens </td></tr></tbody></table>

**Returns**

The amount of pool tokens minted.



## Removing liquidity

removeLiquidity decreases the pool's liquidity and burns the caller's shares in the pool.

```solidity
function removeLiquidity(ISmartToken _poolToken, uint256 _amount, uint256 _minReturn) public nonpayable protected active validPoolToken greaterThanZero greaterThanZero 
returns(uint256)
```

**Arguments**

<table><thead><tr><th width="166.33333333333334">Name</th><th width="158">Type</th><th>Description</th></tr></thead><tbody><tr><td>_poolToken</td><td>ISmartToken</td><td>address of the pool token</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of pool tokens to burn</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return-amount of reserve tokens *</td></tr></tbody></table>

**Returns**

The amount of liquidity removed.
