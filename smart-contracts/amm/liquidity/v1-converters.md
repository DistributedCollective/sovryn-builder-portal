---
description: >-
  On V1 Converters, the same value of both reserve assets need to be provided or
  removed. One LP token corresponds to a share of both reserve assets.
---

# V1 Converters

## Adding Liquidity

addLiquidity increases the pool's liquidity and mints new LP tokens (shares in the pool) to the caller. The assets are to be provided in the same ratio as the pool holds them at the time of transaction execution. If the ratio differs, not all provided tokens will be transferred to the pool.

```solidity
function addLiquidity(IERC20Token[] _reserveTokens, uint256[] _reserveAmounts, uint256 _minReturn) public payable protected active 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_reserveTokens</td><td>IERC20Token[]</td><td>address of each reserve token</td></tr><tr><td>_reserveAmounts</td><td>uint256[]</td><td>amount of each reserve token</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return amount of liquidity pool tokens</td></tr></tbody></table>



## Removing liquidity

removeLiquidity decreases the pool's liquidity and burns the caller's shares in the pool.

```solidity
function removeLiquidity(uint256 _amount, IERC20Token[] _reserveTokens, uint256[] _reserveMinReturnAmounts) public nonpayable protected active 
```

**Arguments**

<table><thead><tr><th width="199.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_amount</td><td>uint256</td><td>amount of liquidity pool tokens to burn in exchange for the underlying reserves.</td></tr><tr><td>_reserveTokens</td><td>IERC20Token[]</td><td>address of each reserve token</td></tr><tr><td>_reserveMinReturnAmounts</td><td>uint256[]</td><td>minimum return-amount of each reserve token</td></tr></tbody></table>
