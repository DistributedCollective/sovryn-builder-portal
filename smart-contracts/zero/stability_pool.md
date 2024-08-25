---
description: >-
  Liquidations that involve a Stability Pool offset burn ZUSD from the Stability Pool’s balance, and reduce the ZUSD debt of the liquidated Line of Credit. The only time ZUSD is transferred to/from a Zero contract, is when a user deposits ZUSD to, or withdraws ZUSD from, the StabilityPool.
---

# Stability Pool Functions - `StabilityPool.sol`

`provideToSP(uint _amount, address _frontEndTag)`: allows stablecoin holders to deposit `_amount` of ZUSD to the Stability Pool. It sends `_amount` of ZUSD from their address to the Pool, and tops up their ZUSD deposit by `_amount` and their tagged frontend’s stake by `_amount`. If the depositor already has a non-zero deposit, it sends their accumulated RBTC gains to their address.

`withdrawFromSP(uint _amount)`: allows a ZUSD holder to withdraw `_amount` of ZUSD from the Stability Pool, up to the value of their remaining Stability Pool deposit. It decreases their ZUSD balance by `_amount`. It sends the depositor’s accumulated RBTC gains to their address. If the user makes a partial withdrawal, their deposit remainder will earn further gains. To prevent potential loss evasion by depositors, withdrawals from the Stability Pool are suspended when there are liquidable Lines of Credit with ICR < 110% in the system.

`withdrawRBTCGainToTrove(address _hint)`: sends the user's entire accumulated RBTC gain to the user's active Line of Credit, and updates their Stability Pool deposit with its accumulated loss from debt absorptions.

`getDepositorRBTCGain(address _depositor)`: returns the accumulated RBTC gain for a given Stability Pool depositor

`getCompoundedZUSDDeposit(address _depositor)`: returns the remaining deposit amount for a given Stability Pool depositor

## Gas compensation

In Zero, we want to maximize liquidation throughput, and ensure that undercollateralized Lines of Credit are liquidated promptly by “liquidators” - agents who may (or may not) also hold Stability Pool deposits, and who expect to profit from liquidations.

However, gas costs in RSK can be substantial. If the gas costs of our public liquidation functions are too high, this may discourage liquidators from calling them, and leave the system holding too many undercollateralized Lines of Credit for too long.

The protocol thus directly compensates liquidators for their gas costs, to incentivize prompt liquidations in both normal and extreme periods of high gas prices. Liquidators should be confident that they will at least break even by making liquidation transactions.

Gas compensation is paid in a mix of ZUSD and RBTC. While the RBTC is taken from the liquidated Line of Credit, the ZUSD is provided by the borrower. When a borrower first issues debt, some ZUSD is reserved as a Liquidation Reserve. A liquidation transaction thus draws RBTC from the Line(s) of Credit it liquidates, and sends both the reserved ZUSD and the compensation in RBTC to the caller, and liquidates the remainder.

When a liquidation transaction liquidates multiple Lines of Credit, each Line of Credit contributes ZUSD and RBTC towards the total compensation for the transaction.

Gas compensation per liquidated Line of Credit is given by the formula:

Gas compensation = `20 ZUSD + 0.5% of Line of Credit’s collateral (RBTC)`

The intentions behind this formula are:
- To ensure that smaller Lines of Credit are liquidated promptly in normal times, at least.
- To ensure that larger Lines of Credit are liquidated promptly even in extreme high gas price periods. The larger the Line of Credit, the stronger the incentive to liquidate it.

### Gas compensation schedule

When a borrower opens a Line of Credit, an additional 20 ZUSD debt is issued, and 20 ZUSD is minted and sent to a dedicated contract (`GasPool`) for gas compensation - the "gas pool".

When a borrower closes their active Line of Credit, this gas compensation is refunded: 20 ZUSD is burned from the gas pool's balance, and the corresponding 20 ZUSD debt on the Line of Credit is cancelled.

The purpose of the 20 ZUSD Liquidation Reserve is to provide a minimum level of gas compensation, regardless of the Line of Credit's collateral size or the current RBTC price.

### Liquidation

When a Line of Credit is liquidated, 0.5% of its collateral is sent to the liquidator, along with the 20 ZUSD Liquidation Reserve. Thus, a liquidator always receives `{20 ZUSD + 0.5% collateral}` per Line of Credit that they liquidate. The collateral remainder of the Line of Credit is then either offset, redistributed, or a combination of both, depending on the amount of ZUSD in the Stability Pool.

### Gas compensation and redemptions

When a Line of Credit is redeemed from, the redemption is made only against (debt - 20), not the entire debt.

But if the redemption causes an amount (debt - 20) to be cancelled, the Line of Credit is then closed: the 20 ZUSD Liquidation Reserve is cancelled with its remaining 20 debt. That is, the gas compensation is burned from the gas pool, and the 20 debt is zeroed. The RBTC collateral surplus from the Line of Credit remains in the system, to be later claimed by its owner.

### Gas compensation helper functions

Gas compensation functions are found in the parent _LiquityBase.sol_ contract:

`_getCollGasCompensation(uint _entireColl)` returns the amount of RBTC to be drawn from a Line of Credit's collateral and sent as gas compensation. 

`_getCompositeDebt(uint _debt)` returns the composite debt (drawn debt + gas compensation) of a Line of Credit, for the purpose of ICR calculation.

# The Stability Pool

Any ZUSD holder may deposit ZUSD to the Stability Pool. It is designed to absorb debt from liquidations, and reward depositors with the liquidated collateral, shared between depositors in proportion to their deposit size.

Since liquidations are expected to occur at an ICR of just below 110%, and even in most extreme cases, still above 100%, a depositor can expect to receive a net gain from most liquidations. When that holds, the dollar value of the RBTC gain from a liquidation exceeds the dollar value of the ZUSD loss (assuming the price of ZUSD is 1 USD).  

We define the **collateral surplus** in a liquidation as `$(RBTC) - debt`, where `$(...)` represents the dollar value.

At a ZUSD price of 1 USD, lines of credit with `ICR > 100%` have a positive collateral surplus.

After one or more liquidations, a deposit will have absorbed ZUSD losses, and received RBTC gains. The remaining reduced deposit is the **compounded deposit**.

Stability Providers expect a positive ROI on their initial deposit. That is:

`$(RBTC Gain + compounded deposit) > $(initial deposit)`

### Mixed liquidations: offset and redistribution

When a liquidation hits the Stability Pool, it is known as an **offset**: the debt of the Line of Credit is offset against the ZUSD in the Stability Pool. When **x** ZUSD debt is offset, the debt is cancelled, and **x** ZUSD in the Stability Pool is burned. When the ZUSD Stability Pool is greater than the debt of the Line of Credit, all the Line of Credit's debt is cancelled, and all its RBTC is shared between depositors. This is a **pure offset**.

It can happen that the ZUSD in the Stability Pool is less than the debt of a Line of Credit. In this case, the the whole Stability Pool will be used to offset a fraction of the Line of Credit’s debt, and an equal fraction of the Line of Credit’s RBTC collateral will be assigned to Stability Providers. The remainder of the Line of Credit’s debt and RBTC gets redistributed to active Lines of Credit. This is a **mixed offset and redistribution**.

Because the RBTC collateral fraction matches the offset debt fraction, the effective ICR of the collateral and debt that is offset, is equal to the ICR of the Line of Credit. So, for depositors, the ROI per liquidation depends only on the ICR of the liquidated Line of Credit.

### Stability Pool deposit losses and RBTC gains - implementation

Deposit functionality is handled by `StabilityPool.sol` (`provideToSP`, `withdrawFromSP`, etc).  StabilityPool also handles the liquidation calculation, and holds the ZUSD and RBTC balances.

When a liquidation is offset with the Stability Pool, debt from the liquidation is cancelled with an equal amount of ZUSD in the pool, which is burned. 

Individual deposits absorb the debt from the liquidated Line of Credit in proportion to their deposit as a share of total deposits.
 
Similarly the liquidated Line of Credit’s RBTC is assigned to depositors in the same proportion.

For example: a liquidation that empties 30% of the Stability Pool will reduce each deposit by 30%, no matter the size of the deposit.

### Stability Pool example

Here’s an example of the Stability Pool absorbing liquidations. The Stability Pool contains 3 depositors, A, B and C, and the RBTC:USD price is 100.

There are two Lines of Credit to be liquidated, T1 and T2:

|     | Line of Credit | Collateral (RBTC) | Debt (ZUSD) | ICR         | $(RBTC) ($) | Collateral surplus ($) |
| --- | -------------- | ----------------- | ----------- | ----------- | ----------- | ---------------------- |
|     | T1             | 1.6               | 150         | 1.066666667 | 160         | 10                     |
|     | T2             | 2.45              | 225         | 1.088888889 | 245         | 20                     |

Here are the deposits, before any liquidations occur:

| Depositor | Deposit | Share  |
| --------- | ------- | ------ |
| A         | 100     | 0.1667 |
| B         | 200     | 0.3333 |
| C         | 300     | 0.5    |
| Total     | 600     | 1      |

Now, the first liquidation T1 is absorbed by the Pool: 150 debt is cancelled with 150 Pool ZUSD, and its 1.6 RBTC is split between depositors. We see the gains earned by A, B, C, are in proportion to their share of the total ZUSD in the Stability Pool:

| Deposit | Debt absorbed from T1 | Deposit after | Total RBTC gained | $(deposit + RBTC gain) ($) | Current ROI   |
| ------- | --------------------- | ------------- | ----------------- | -------------------------- | ------------- |
| A       | 25                    | 75            | 0.2666666667      | 101.6666667                | 0.01666666667 |
| B       | 50                    | 150           | 0.5333333333      | 203.3333333                | 0.01666666667 |
| C       | 75                    | 225           | 0.8               | 305                        | 0.01666666667 |
| Total   | 150                   | 450           | 1.6               | 610                        | 0.01666666667 |

And now the second liquidation, T2, occurs: 225 debt is cancelled with 225 Pool ZUSD, and 2.45 RBTC is split between depositors. The accumulated RBTC gain includes all RBTC gain from T1 and T2.

| Depositor | Debt absorbed from T2 | Deposit after | Accumulated RBTC | $(deposit + RBTC gain) ($) | Current ROI |
| --------- | --------------------- | ------------- | ---------------- | -------------------------- | ----------- |
| A         | 37.5                  | 37.5          | 0.675            | 105                        | 0.05        |
| B         | 75                    | 75            | 1.35             | 210                        | 0.05        |
| C         | 112.5                 | 112.5         | 2.025            | 315                        | 0.05        |
| Total     | 225                   | 225           | 4.05             | 630                        | 0.05        |

It’s clear that:

- Each depositor gets the same ROI from a given liquidation
- Depositors return increases over time, as the deposits absorb liquidations with a positive collateral surplus

Eventually, a deposit can be fully “used up” in absorbing debt, and reduced to 0. This happens whenever a liquidation occurs that empties the Stability Pool. A deposit stops earning RBTC gains when it has been reduced to 0.

### Stability Pool implementation

A depositor obtains their compounded deposits and corresponding RBTC gain in a “pull-based” manner. The system calculates the depositor’s compounded deposit and accumulated RBTC gain when the depositor makes an operation that changes their ZUSD deposit.

Depositors deposit ZUSD via `provideToSP`, and withdraw with `withdrawFromSP`. Their accumulated RBTC gain is paid out every time they make a deposit operation - so RBTC payout is triggered by both deposit withdrawals and top-ups.

