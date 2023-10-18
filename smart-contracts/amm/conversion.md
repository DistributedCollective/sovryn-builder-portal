---
description: >-
  Swaps happen according to the provided conversion path and can involve one or
  multiple liquidity pools.
---

# Conversion

## Conversion Path

conversionPath returns the conversion path between two tokens in the network. Please note that this method is quite expensive in terms of gas and should generally be called off-chain. Moreover, the function returns the shortest path, not necessarily the one with the best rate.&#x20;

```solidity
function conversionPath(IERC20Token _sourceToken, IERC20Token _targetToken) public view
returns(address[])
```

**Arguments**

<table><thead><tr><th width="198.33333333333334">Name</th><th width="179">Type</th><th>Description</th></tr></thead><tbody><tr><td>_sourceToken</td><td>IERC20Token</td><td>source token address</td></tr><tr><td>_targetToken</td><td>IERC20Token</td><td>target token address </td></tr></tbody></table>

**Returns**

The conversion path between the two tokens. Format:

```solidity
[source token, converter anchor, target token, converter anchor, target token...]
```



## Conversion Rate

rateByPath returns the expected target amount of converting a given amount on a given path.

```solidity
function rateByPath(IERC20Token[] _path, uint256 _amount) public view
returns(uint256)
```

**Arguments**

<table><thead><tr><th width="172.33333333333334">Name</th><th width="186">Type</th><th>Description</th></tr></thead><tbody><tr><td>_path</td><td>IERC20Token[]</td><td>conversion path (see conversion path format above)</td></tr><tr><td>_amount</td><td>uint256</td><td>amount of _path[0] tokens received from the sender</td></tr></tbody></table>

**Returns**

The expected target amount.



## Conversion

convertByPath converts the token to any other token in the Sovryn swap network by following a predefined conversion path and transfers the result tokens to a target account. Affiliate account/fee can also be passed in to receive a conversion fee (on top of the liquidity provider fees). Please note that the network should already have been given allowance of the source token.

```solidity
function convertByPath(IERC20Token[] _path, uint256 _amount, uint256 _minReturn, address _beneficiary, address _affiliateAccount, uint256 _affiliateFee) public payable protected greaterThanZero 
returns(uint256)
```

**Arguments**

<table><thead><tr><th width="180.33333333333334">Name</th><th width="161">Type</th><th>Description</th></tr></thead><tbody><tr><td>_path</td><td>IERC20Token[]</td><td>conversion path, see conversion path format above</td></tr><tr><td>_amount</td><td>uint256</td><td>amount to convert from, in the source token</td></tr><tr><td>_minReturn</td><td>uint256</td><td>if the conversion results in an amount smaller than the minimum return - it is cancelled, must be greater than zero</td></tr><tr><td>_beneficiary</td><td>address</td><td>account that will receive the conversion result or 0x0 to send the result to the sender account</td></tr><tr><td>_affiliateAccount</td><td>address</td><td>wallet address to receive the affiliate fee or 0x0 to disable affiliate fee</td></tr><tr><td>_affiliateFee</td><td>uint256</td><td>affiliate fee in PPM or 0 to disable affiliate fee *</td></tr></tbody></table>

**Returns**

The amount of tokens to receive from the conversion.
