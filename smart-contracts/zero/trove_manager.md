---
description: >-
  Redemptions burn ZUSD from the redeemer’s balance, and reduce the debt of the Line of Credit redeemed against.
---

# Line of Credit Manager Functions - `TroveManager.sol`

`liquidate(address _borrower)`: callable by anyone, attempts to liquidate the Line of Credit of `_user`. Executes successfully if `_user`’s Line of Credit meets the conditions for liquidation (e.g. in Normal Mode, it liquidates if the Line of Credit's ICR < the system Critical Collateral Ratio [CCR]).  

`liquidateTroves(uint n)`: callable by anyone, checks for under-collateralized Lines of Credit below MCR and liquidates up to `n`, starting from the Line of Credit  with the lowest collateralization ratio; subject to gas constraints and the actual number of under-collateralized Lines of Credit. The gas costs of `liquidateTroves(uint n)` mainly depend on the number of Lines of Credit that are liquidated, and whether the Lines of Credit are offset against the Stability Pool or redistributed. For n=1, the gas costs per liquidated Line of Credit are roughly between 215K-400K, for n=5 between 80K-115K, for n=10 between 70K-82K, and for n=50 between 60K-65K.

`batchLiquidateTroves(address[] calldata _troveArray)`: callable by anyone, accepts a custom list of Line of Credit addresses as an argument. Steps through the provided list and attempts to liquidate every Line of Credit, until it reaches the end or it runs out of gas. A Line of Credit is liquidated only if it meets the conditions for liquidation. For a batch of 10 Lines of Credit, the gas costs per liquidated Line of Credit are roughly between 75K-83K, for a batch of 50 Lines of Credit between 54K-69K.

`redeemCollateral(uint _ZUSDAmount, address _firstRedemptionHint, address _upperPartialRedemptionHint, address _lowerPartialRedemptionHint, uint _partialRedemptionHintNICR, uint _maxIterations, uint _maxFeePercentage)`: redeems `_ZUSDamount` of ZUSD for RBTC from the system. Decreases the caller’s ZUSD balance, and sends them the corresponding amount of RBTC. Executes successfully if the caller has sufficient ZUSD to redeem. The number of Lines of Credit redeemed from is capped by `_maxIterations`. The borrower has to provide a `_maxFeePercentage` that he/she is willing to accept in case of a fee slippage i.e. when another redemption transaction is processed first, driving up the redemption fee.

`getCurrentICR(address _user, uint _price)`: computes the user’s individual collateralization ratio (ICR) based on their total collateral and total ZUSD debt. Returns 2^256 -1 if they have 0 debt.

`getTroveOwnersCount()`: get the number of active lines of credit in the system.

`getPendingRBTCReward(address _borrower)`: get the pending RBTC reward from liquidation redistribution events, for the given Line of Credit .

`getPendingZUSDDebtReward(address _borrower)`: get the pending Line of Credit debt "reward" (i.e. the amount of extra debt assigned to the Line of Credit) from liquidation redistribution events.

`getEntireDebtAndColl(address _borrower)`: returns a Line of Credit’s entire debt and collateral balance, which respectively include any pending debt rewards and RBTC rewards from prior redistributions.

`getEntireSystemColl()`:  Returns the systemic entire collateral allocated to Lines of Credit i.e. the sum of the RBTC in the Active Pool and the Default Pool.

`getEntireSystemDebt()` Returns the systemic entire debt assigned to Lines of Credit i.e. the sum of the ZUSDDebt in the Active Pool and the Default Pool.

`getTCR()`: returns the total collateralization ratio (TCR) of the system. The TCR is based on the the entire system debt and collateral (including pending rewards).

`checkRecoveryMode()`: reveals whether or not the system is in Recovery Mode (i.e. whether the Total Collateralization Ratio (TCR) is below the Critical Collateralization Ratio (CCR)).

## Hints for `redeemCollateral`

`TroveManager::redeemCollateral` as a special case requires additional hints:
- `_firstRedemptionHint` hints at the position of the first Line of Credit that will be redeemed from,
- `_lowerPartialRedemptionHint` hints at the `nextId` neighbor of the last redeemed Line of Credit upon reinsertion, if it's partially redeemed,
- `_upperPartialRedemptionHint` hints at the `prevId` neighbor of the last redeemed Line of Credit upon reinsertion, if it's partially redeemed,
- `_partialRedemptionHintNICR` ensures that the transaction won't run out of gas if neither `_lowerPartialRedemptionHint` nor `_upperPartialRedemptionHint` are  valid anymore.

`redeemCollateral` will only redeem from Lines of Credit that have an ICR >= MCR. In other words, if there are Lines of Credit at the bottom of the SortedTroves list that are below the minimum collateralization ratio (which can happen after an RBTC:USD price drop), they will be skipped. To make this more gas-efficient, the position of the first redeemable Line of Credit should be passed as `_firstRedemptionHint`.

#### First redemption hint

The first redemption hint is the address of the Line of Credit from which to start the redemption sequence i.e the address of the first Line of Credit in the system with ICR >= 110%.

If when the transaction is confirmed the address is in fact not valid - the system will start from the lowest ICR Line of Credit in the system, and step upwards until it finds the first Line of Credit with ICR >= 110% to redeem from. In this case, since the number of lines of credit below 110% will be limited due to ongoing liquidations, there's a good chance that the redemption transaction still succeed. 

#### Partial redemption hints

All Lines of Credit that are fully redeemed from in a redemption sequence are left with zero debt, and are closed. The remaining collateral (the difference between the orginal collateral and the amount used for the redemption) will be claimable by the owner.

It’s likely that the last Line of Credit in the redemption sequence would be partially redeemed from i.e. only some of its debt cancelled with ZUSD. In this case, it should be reinserted somewhere between top and bottom of the list. The `_lowerPartialRedemptionHint` and `_upperPartialRedemptionHint` hints passed to `redeemCollateral` describe the future neighbors the expected reinsert position.

However, if between the off-chain hint computation and on-chain execution a different transaction changes the state of a Line of Credit that would otherwise be hit by the redemption sequence, then the off-chain hint computation could end up totally inaccurate. This could lead to the whole redemption sequence reverting due to out-of-gas error.

To mitigate this, another hint needs to be provided: `_partialRedemptionHintNICR`, the expected nominal ICR of the final partially-redeemed-from Line of Credit. The on-chain redemption function checks whether, after redemption, the nominal ICR of this Line of Credit would equal the nominal ICR hint.

If not, the redemption sequence doesn’t perform the final partial redemption, and terminates early. This ensures that the transaction doesn’t revert, and most of the requested ZUSD redemption can be fulfilled.

#### Example Redemption with hints
```
 // Get the redemptions hints from the deployed HintHelpers contract
  const redemptionhint = await hintHelpers.getRedemptionHints(ZUSDAmount, price, 50)

  const { 0: firstRedemptionHint, 1: partialRedemptionNewICR, 2: truncatedZUSDAmount } = redemptionhint

  // Get the approximate partial redemption hint
  const { hintAddress: approxPartialRedemptionHint } = await contracts.hintHelpers.getApproxHint(partialRedemptionNewICR, numTrials, 42)
  
  /* Use the approximate partial redemption hint to get the exact partial redemption hint from the 
  * deployed SortedTroves contract
  */
  const exactPartialRedemptionHint = (await sortedTroves.findInsertPosition(partialRedemptionNewICR,
    approxPartialRedemptionHint,
    approxPartialRedemptionHint))

  /* Finally, perform the on-chain redemption, passing the truncated ZUSD amount, the correct hints, and the expected
  * ICR of the final partially redeemed Line of Credit in the sequence. 
  */
  await Line of Credit Manager.redeemCollateral(truncatedZUSDAmount,
    firstRedemptionHint,
    exactPartialRedemptionHint[0],
    exactPartialRedemptionHint[1],
    partialRedemptionNewICR,
    0, maxFee,
    { from: redeemer },
  )
```
