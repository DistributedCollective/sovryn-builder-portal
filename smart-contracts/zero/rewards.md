---
description: >-
  We use a highly scalable method of tracking deposits and RBTC rewards that has O(1) complexity.
---

# [REWARD SYSTEM ](https://github.com/DistributedCollective/zero/blob/main/README.md#zero-system-fees) 

We use a highly scalable method of tracking deposits and RBTC rewards that has O(1) complexity. A mathematical manipulation allows us to factor out the initial deposit, and accurately track all depositors’ compounded deposits and accumulated RBTC gains over time, as liquidations occur, using just these two variables: a product and a sum.  

When a liquidation occurs, rather than updating each depositor’s deposit and RBTC gain, we simply update two intermediate variables: a product `P`, and a sum `S`.  

The formula for a depositor’s accumulated RBTC gain is derived here:

[Scalable reward distribution for compounding, decreasing stake](https://github.com/DistributedCollective/zero/blob/main/packages/contracts/mathProofs/Scalable%20Compounding%20Stability%20Pool%20Deposits.pdf)

Each liquidation updates `P` and `S`. After a series of liquidations, a compounded deposit and corresponding RBTC gain can be calculated using the initial deposit, the depositor’s snapshots, and the current values of `P` and `S`.

Any time a depositor updates their deposit (withdrawal, top-up) their RBTC gain is paid out, and they receive new snapshots of `P` and `S`.

This is similar in spirit to the simpler [Scalable Reward Distribution on the Ethereum Network by Bogdan Batog et al](http://batog.info/papers/scalable-reward-distribution.pdf), however, the mathematics is more involved as we handle a compounding, decreasing stake, and a corresponding RBTC reward.

## Zero System Fees

Zero generates fee revenue from certain operations. Fees are captured by SOV stakers.

An SOV holder may stake their SOV, and earn a share of all system fees, proportional to their share of the total SOV staked. This is described in the [Sovryn governance documentation](../bitocracy/README.md).

Zero generates revenue in two ways: redemptions, and borrowing ZUSD.

Redemptions fees are paid in RBTC. Borrowing fees (when a user opens a Line of Credit, or borrows more ZUSD from their existing Line of Credit) are paid in ZUSD.

### Redemption Fee

The redemption fee is taken as a cut of the total RBTC drawn from the system in a redemption. It is based on the current redemption rate.

In the `TroveManager`, `redeemCollateral` calculates the RBTC fee and transfers it to the `sovFeeCollector`.

### Borrowing fee

The borrowing fee is charged on the ZUSD drawn by the user and is added to the Line of Credit's ZUSD debt. It is based on the current borrowing rate.

When new ZUSD are drawn via one of the `BorrowerOperations` functions `openTrove`, `withdrawZUSD` or `adjustTrove`, an extra amount `ZUSDFee` is minted, and an equal amount of debt is added to the user’s Line of Credit. The `ZUSDFee` is transferred to the `sovFeeCollector`.

### Fee Schedule

Redemption and issuance fees are based on the `baseRate` state variable in TroveManager, which is dynamically updated. The `baseRate` increases with each redemption, and decays according to time passed since the last fee event i.e. the last redemption or issuance of ZUSD.

The current fee schedule:

Upon each redemption:
- `baseRate` is decayed based on time passed since the last fee event
- `baseRate` is incremented by an amount proportional to the fraction of the total ZUSD supply that was redeemed
- The redemption rate is given by `min{REDEMPTION_FEE_FLOOR + baseRate * RBTCdrawn, DECIMAL_PRECISION}`

Upon each debt issuance:
- `baseRate` is decayed based on time passed since the last fee event
- The borrowing rate is given by `min{BORROWING_FEE_FLOOR + baseRate * newDebtIssued, MAX_BORROWING_FEE}`

`REDEMPTION_FEE_FLOOR` and `BORROWING_FEE_FLOOR` are both set to 0.5%, while `MAX_BORROWING_FEE` is 5% and `DECIMAL_PRECISION` is 100%.

### Intuition behind fees

The larger the redemption volume, the greater the fee percentage.

The longer the time delay since the last operation, the more the `baseRate` decreases.

The intent is to throttle large redemptions with higher fees, and to throttle borrowing directly after large redemption volumes. The `baseRate` decay over time ensures that the fee for both borrowers and redeemers will “cool down”, while redemptions volumes are low.

Furthermore, the fees cannot become smaller than 0.5%, which in the case of redemptions protects the redemption facility from being front-run by arbitrageurs that are faster than the price feed. The 5% maximum on the issuance is meant to keep the system (somewhat) attractive for new borrowers even in phases where the monetary supply is contracting due to redemptions.

### Fee decay Implementation

Time is measured in units of minutes. The `baseRate` decay is based on `block.timestamp - lastFeeOpTime`. If less than a minute has passed since the last fee event, then `lastFeeOpTime` is not updated. This prevents “base rate griefing” i.e. it prevents an attacker stopping the `baseRate` from decaying by making a series of redemptions or issuing ZUSD with time intervals of < 1 minute.

The decay parameter is tuned such that the fee changes by a factor of 0.99 per hour, i.e. it loses 1% of its current value per hour. At that rate, after one week, the baseRate decays to 18% of its prior value. The exact decay parameter is subject to change, and will be fine-tuned via economic modelling.

### Staking SOV and earning fees

SOV holders may `stake` and `unstake` their SOV in the `Staking` contract in exchange for Voting Power. 

When a fee event occurs, the fee in ZUSD or RBTC is sent to the staking contract, and a reward-per-unit-staked sum (`F_RBTC`, or `F_ZUSD`) is incremented. An SOV stake earns a share of the fee equal to its share of the total SOV Voting Power, at the instant the fee occurred.

This staking formula and implementation follows the basic [“Batog” pull-based reward distribution](http://batog.info/papers/scalable-reward-distribution.pdf).

## Redistributions and Corrected Stakes

When a liquidation occurs and the Stability Pool is empty or smaller than the liquidated debt, the redistribution mechanism should distribute the remaining collateral and debt of the liquidated Line of Credit to all active Lines of Credit in the system, in proportion to their collateral.

For two Lines of Credit A and B with collateral `A.coll > B.coll`, Line of Credit A should earn a bigger share of the liquidated collateral and debt.

In Zero it is important that all active Lines of Credit remain ordered by their ICR. We have proven that redistribution of the liquidated debt and collateral proportional to active Lines of Credit collateral preserves the ordering of active Lines of Credit by ICR as liquidations occur over time. Please see the [proofs section](https://github.com/DistributedCollective/zero/tree/main/packages/contracts/mathProofs).

However, when it comes to implementation, RSK gas costs make it too expensive to loop over all Lines of Credit and write new data to storage for each one. When a Line of Credit receives redistribution rewards, the system does not update the Line of Credit's collateral and debt properties - instead, the Line of Credit’s rewards remain "pending" until the borrower's next operation.

These “pending rewards” can not be accounted for in future reward calculations in a scalable way.

However: the ICR of a Line of Credit is always calculated as the ratio of its total collateral to its total debt. So, a Line of Credit’s ICR calculation **does** include all its previous accumulated rewards.

**This causes a problem: redistributions proportional to initial collateral can break Line of Credit ordering.**

Consider the case where new Line of Credit is created after all active Lines of Credit have received a redistribution from a liquidation. This “fresh” Line of Credit  has then experienced fewer rewards than the older Lines of Credit, and thus, it receives a disproportionate share of subsequent rewards, relative to its total collateral.

The fresh Line of Credit would earn rewards based on its **entire** collateral, whereas old lines of credit would earn rewards based only on **some portion** of their collateral - since a part of their collateral is pending, and not included in the Line of Credit’s `coll` property.

This can break the ordering of Lines of Credit by ICR - see the [proofs section](https://github.com/DistributedCollective/zero/tree/main/packages/contracts/mathProofs).

### Corrected Stake Solution

We use a corrected stake to account for this discrepancy, and ensure that newer Lines of Credit earn the same liquidation rewards per unit of total collateral, as do older Lines of Credit with pending rewards. Thus the corrected stake ensures the sorted list remains ordered by ICR, as liquidation events occur over time.

When a Line of Credit is opened, its stake is calculated based on its collateral, and snapshots of the entire system collateral and debt which were taken immediately after the last liquidation.

A Line of Credit’s stake is given by:

```javascript
stake = _coll.mul(totalStakesSnapshot).div(totalCollateralSnapshot)
```

It then earns redistribution rewards based on this corrected stake. A newly opened Line of Credit’s stake will be less than its raw collateral, if the system contains active Lines of Credit with pending redistribution rewards when it was made.

Whenever a borrower adjusts their Line of Credit’s collateral, their pending rewards are applied, and a fresh corrected stake is computed.

To convince yourself this corrected stake preserves ordering of active Lines of Credit by ICR, please see the [proofs section](https://github.com/DistributedCollective/zero/blob/main/papers).

## Math Proofs

The Zero implementation relies on some important system properties and mathematical derivations.

In particular, we have:

- Proofs that Line of Credit ordering is maintained throughout a series of liquidations and new Line of Credit openings
- A derivation of a formula and implementation for a highly scalable (O(1) complexity) reward distribution in the Stability Pool, involving compounding and decreasing stakes.

PDFs of these can be found in https://github.com/DistributedCollective/zero/blob/main/papers