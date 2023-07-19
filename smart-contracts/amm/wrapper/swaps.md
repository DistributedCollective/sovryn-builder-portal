---
description: >-
  The wrapper contract can be used to directly swap to or from RBTC without
  requiring an additional transaction to wrap or unwrap WRBTC.
---

# Swaps

convertByPath converts the token to any other token in the sovryn swap network by following a predefined conversion path and transfers the result tokens to a target account. Please note that either the source or target token address has to be WRBTC. If the source is not RBTC, the wrapper contract should already have been given allowance of the source token. The wrapping or unwrapping of WRBTC is handled the wrapper contract.

```solidity
function convertByPath(IERC20Token[] memory _path, uint256 _amount, uint256 _minReturn) 
returns(uint256) 
```

**Arguments**

<table><thead><tr><th width="180.33333333333334">Name</th><th width="161">Type</th><th>Description</th></tr></thead><tbody><tr><td>_path</td><td>IERC20Token[]</td><td>conversion path, see conversion path format <a href="../conversion.md">here</a></td></tr><tr><td>_amount</td><td>uint256</td><td>amount to convert from, in the source token</td></tr><tr><td>_minReturn</td><td>uint256</td><td>if the conversion results in an amount smaller than the minimum return - it is cancelled, must be greater than zero</td></tr></tbody></table>

**Returns**

The amount of tokens to receive from the conversion.
