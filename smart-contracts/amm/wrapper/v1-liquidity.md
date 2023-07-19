---
description: >-
  Providing liquidity with the wrapper has the benefit that RBTC is
  automatically wrapped to WRBTC and the pool tokens are automatically deposited
  at the liquidity mining contract.
---

# V1 liquidity

## Adding liquidity

addLiquidityToV1 accepts RBTC, wraps them to WRBTC, adds liquidity to the converter contract and transfers the pool tokens to the liquidity mining contract.

```solidity
function addLiquidityToV1(
        address _liquidityPoolConverterAddress,
        IERC20Token[] memory _reserveTokens, 
        uint256[] memory _reserveAmounts, 
        uint256 _minReturn)  
```

**Arguments**

<table><thead><tr><th width="227.33333333333334">Name</th><th width="154">Type</th><th>Description</th></tr></thead><tbody><tr><td>_liquidityPoolConverterAddress</td><td>address</td><td>address of LiquidityPoolConverter contract</td></tr><tr><td>_reserveTokens</td><td>address[]</td><td>address of each reserve token</td></tr><tr><td>_reserveAmounts</td><td>uint256[]</td><td>amount of each reserve token</td></tr><tr><td>_minReturn</td><td>uint256</td><td>minimum return amount of liquidity pool tokens</td></tr></tbody></table>





## Removing liquidity

removeLiquidityFromV1 withdraws the pool tokens from the liquidity mining contract, removes the liquidity from the converter contract, unwraps the WRBTC to RBTC and sends the reserve tokens to the user.&#x20;

```solidity
function removeLiquidityFromV1(
        address _liquidityPoolConverterAddress,
        uint256 _amount, 
        IERC20Token[] memory _reserveTokens, 
        uint256[] memory _reserveMinReturnAmounts)
```

**Arguments**

<table><thead><tr><th width="225.33333333333334">Name</th><th width="159">Type</th><th>Description</th></tr></thead><tbody><tr><td>_liquidityPoolConverterAddress</td><td>address</td><td>address of LiquidityPoolConverter contract</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of liquidity pool tokens to burn in exchange for the underlying reserves.</td></tr><tr><td>_reserveTokens</td><td>IERC20Token[]</td><td>address of each reserve token</td></tr><tr><td>_reserveMinReturnAmounts</td><td>uint256[]</td><td>minimum return-amount of each reserve token</td></tr></tbody></table>
