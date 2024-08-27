---
description: >-
  Zero is a collateralized debt platform. Users can lock up collateral (RBTC), issue stablecoins (ZUSD) to their own RSK address, and subsequently transfer those stablecoins to any other RSK address.
---

# [ZERO PROTOCOL](https://github.com/DistributedCollective/zero/blob/main/README.md#zero-overview)

Zero is a collateralized debt platform. Users can lock up collateral (RBTC), issue stablecoins (ZUSD) to their own RSK address, and subsequently transfer those stablecoins to any other RSK address. The individual collateralized debt positions are called Lines of Credit (aka Troves).

[Source](https://github.com/DistributedCollective/zero/blob/main/README.md#zero-overview).

The stablecoins are economically geared towards maintaining a value of 1 ZUSD = 1 USD, due to the following properties:

1. The system is always designed to be over-collateralized - the dollar value of the locked RBTC exceeds the dollar value of the issued ZUSD

2. The stablecoins are fully redeemable - users can always swap $x worth of ZUSD for $x worth of RBTC (minus fees) directly with the system.

3. The system algorithmically controls the generation of ZUSD through a variable borrowing fee.

After opening a Line of Credit with some RBTC, users may issue ("borrow") tokens such that the collateral ratio of their Line of Credit remains above 110% e.g. a user with $1000 worth of RBTC in a Line of Credit can issue up to 909.09 ZUSD.

ZUSD is freely transferable - anyone with an RSK address can send or receive ZUSD tokens, whether they have an open Line of Credit or not. The ZUSD are burned upon repayment of a Line of Credit's debt.

The Zero system regularly updates the RBTC:USD price via a decentralized data feed. When a Line of Credit falls below a minimum collateralization ratio (MCR) of 110%, it is considered under-collateralized and is vulnerable to liquidation.

## Liquidation and the Stability Pool

Zero utilizes a two-step liquidation mechanism in the following order of priority: 

1. Offset under-collateralized lines of credit against the Stability Pool containing ZUSD tokens

2. Redistribute under-collateralized lines of credit to other borrowers if the Stability Pool is emptied

Zero primarily uses the ZUSD tokens in its Stability Pool to absorb the under-collateralized debt i.e. to repay the liquidated borrower's liability.

Any user may deposit ZUSD tokens to the Stability Pool. This allows them to earn the collateral from the liquidated Line of Credit. When a liquidation occurs, the liquidated debt is canceled with the same amount of ZUSD in the Stability Pool (which is burned as a result). The liquidated RBTC is then proportionally distributed to Stability Pool depositors.

Stability Pool depositors can expect to earn net gains from liquidations. In most cases, the value of the liquidated RBTC will be greater than the value of the canceled debt (since a liquidated Line of Credit will likely have an individiual collateralization ratio [ICR] just slightly below 110%).

Suppose the liquidated debt is higher than the amount of ZUSD in the Stability Pool. In that case, the system tries to cancel as much debt as possible with the ZUSD from the Stability Pool. Then, the system redistributes the remaining liquidated collateral and debt across all active Lines of Credit.

Anyone may call the public `liquidateTroves()` function, which will check for under-collateralized Lines of Credit and liquidate them. Alternatively, they can call `batchLiquidateTroves()` with a custom list of Line of Credit addresses to attempt to liquidate.

### Liquidation gas costs

Currently, mass liquidations performed via the above functions cost 60-65k gas per Line of Credit. Thus the system can liquidate up to a maximum of 95-105 Lines of Credit in a single transaction.

### Liquidation Logic

The precise behavior of liquidations depends on the ICR of the Line of Credit being liquidated and global system conditions: the total collateralization ratio (TCR) of the system, the size of the Stability Pool, etc.

Here is the liquidation logic for a single Line of Credit in Normal Mode and Recovery Mode. `SP.ZUSD` represents the ZUSD in the Stability Pool.

#### Liquidations in Normal Mode: TCR >= 150%

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Condition | Liquidation behavior                                                                                                                                                                                                                                                                                                                           |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ICR < MCR & SP.ZUSD >= Line of Credit.debt                                                                                                                                                                                                                                                                                                                                        | ZUSD in the StabilityPool equal to the Line of Credit's debt is offset with the Line of Credit's debt. The Line of Credit's RBTC collateral is shared between depositors.                                                                                                                                                                      |
| ICR < MCR & SP.ZUSD < Line of Credit.debt                                                                                                                                                                                                                                                                                                                                         | The total StabilityPool ZUSD is offset with an equal amount of debt from the Line of Credit. A fraction of the Line of Credit's collateral (equal to the ratio of its offset debt to its entire debt) is shared between depositors. The remaining debt and collateral (minus RBTC gas compensation) is redistributed to active Lines of Credit |
| ICR < MCR & SP.ZUSD = 0                                                                                                                                                                                                                                                                                                                                                           | Redistribute all debt and collateral (minus RBTC gas compensation) to activate the Line of Credit.                                                                                                                                                                                                                                             |
|                                                                                                                                                                                                                                                                                                                                                                                   |
| ICR  >= MCR                                                                                                                                                                                                                                                                                                                                                                       | Do nothing.                                                                                                                                                                                                                                                                                                                                    |  

#### Liquidations in Recovery Mode: TCR < 150%  

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Condition | Liquidation behavior                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ICR <=100%                                                                                                                                                                                                                                                                                                                                                                        | Redistribute all debt and collateral (minus RBTC gas compensation) to active the Line of Credit.                                                                                                                                                                                                                                                                                                                                       |
| 100% < ICR < MCR & SP.ZUSD > Line of Credit.debt                                                                                                                                                                                                                                                                                                                                  | ZUSD in the StabilityPool equal to the Line of Credit's debt is offset with the Line of Credit's debt. The Line of Credit's RBTC collateral (minus RBTC gas compensation) is shared between depositors.                                                                                                                                                                                                                                |
| 100% < ICR < MCR & SP.ZUSD < Line of Credit.debt                                                                                                                                                                                                                                                                                                                                  | The total StabilityPool ZUSD is offset with an equal amount of debt from the Line of Credit. A fraction of the Line of Credit's collateral (equal to the ratio of its offset debt to its entire debt) is shared between depositors. The remaining debt and collateral (minus RBTC gas compensation) is redistributed to active lines of credit                                                                                         |
| MCR <= ICR < TCR & SP.ZUSD >= Line of Credit.debt                                                                                                                                                                                                                                                                                                                                 | The StabilityPool ZUSD is offset with an equal amount of debt from the Line of Credit. A fraction of RBTC collateral with a dollar value equal to `1.1 * debt` is shared between depositors. Nothing is redistributed to another active Line of Credit. Since its ICR was > 1.1, the Line of Credit has a collateral remainder, which is sent to the `CollSurplusPool` and is claimable by the borrower. The Line of Credit is closed. |
| MCR <= ICR < TCR & SP.ZUSD  < Line of Credit .debt                                                                                                                                                                                                                                                                                                                                | Do nothing.                                                                                                                                                                                                                                                                                                                                                                                                                            |
| ICR >= TCR                                                                                                                                                                                                                                                                                                                                                                        | Do nothing.                                                                                                                                                                                                                                                                                                                                                                                                                            |

## Gains From Liquidations

Stability Pool depositors gain RBTC over time, as liquidated debt is canceled with their deposit. When they withdraw all or part of their deposited tokens or top up their deposit, the system sends them their accumulated RBTC gains.

Similarly, a Line of Credit's accumulated gains from liquidations are automatically applied to the Line of Credit when the owner performs any operation e.g. adding/withdrawing collateral or borrowing/repaying ZUSD.

## ZUSD Redemption

Any ZUSD holder (whether or not they have an active Line of Credit) may redeem their ZUSD directly with the system. Their ZUSD is exchanged for RBTC, at face value: redeeming x ZUSD tokens returns $x worth of RBTC (minus a [redemption fee](rewards.md)).

When ZUSD is redeemed for RBTC, the system cancels the ZUSD with debt from a Line of Credit, and the RBTC is drawn from the Line of Credit's collateral.

To fulfill the redemption request, Lines of Credit are redeemed in ascending order of their collateralization ratio.

A redemption sequence of `n` steps will **fully** redeem from up to `n-1` Line of Credit, and **partially** redeems from up to 1 Line of Credit, which is always the last Line of Credit in the redemption sequence.

Redemptions are blocked when TCR < 110% (there is no need to restrict ICR < TCR). At that point, TCR redemptions would likely be unprofitable, as ZUSD is probably trading above $1 if the system has crashed that badly, but it could be a way for an attacker with a lot of ZUSD to lower the TCR even further.

Note that redemptions are disabled during the first 14 days of operation since the deployment of the Zero protocol to protect the monetary system in its infancy.

### Partial redemption

Most redemption transactions will include a partial redemption since the amount redeemed is unlikely to perfectly match the total debt of a series of Lines of Credit.

The partially redeemed Line of Credit is re-inserted into the sorted list of Lines of Credit and remains active, with reduced collateral and debt.

### Full redemption

A Line of Credit is defined as "fully redeemed from" when the redemption has caused (debt-20) of its debt to absorb (debt-20) ZUSD. Then, its 20 ZUSD Liquidation Reserve is canceled with its remaining 20 debt: the Liquidation Reserve is burned from the gas address, and the 20 debt is zeroed.

Before closing, we must handle the Line of Credit **collateral surplus**: that is, the excess RBTC collateral remaining after redemption due to its initial over-collateralization.

This collateral surplus is sent to the `CollSurplusPool`, and the borrower can reclaim it later. The Line of Credit is then fully closed.

### Redemptions create a price floor

Economically, the redemption mechanism creates a hard price floor for ZUSD, ensuring that the market price of ZUSD stays at or near 1 USD. 

## Recovery Mode

Recovery Mode kicks in when the system's total collateralization ratio (TCR) falls below 150%.

During Recovery Mode, liquidation conditions are relaxed, and the system blocks borrower transactions that would further decrease the TCR. New ZUSD may only be issued by adjusting existing Lines of Credit to improve their ICR or by opening a new Line of Credit with an ICR of >=150%. In general, if an existing Line of Credit's adjustment reduces its ICR, the transaction is only executed if the resulting TCR is above 150%

Recovery Mode is structured to incentivize borrowers to behave in ways that promptly raise the TCR back above 150%, and to incentivize ZUSD holders to replenish the Stability Pool.

Economically, Recovery Mode is designed to encourage collateral top-ups and debt repayments. It also acts as a self-negating deterrent: the possibility of it occurring actually guides the system away from ever reaching it.


## Some Definitions

_**Line of Credit:**_ a collateralized debt position, aka "Trove", bound to a single RSK address. Also referred to as a “CDP” in similar protocols.

_**ZUSD**_:  The stablecoin that may be issued from a user's collateralized debt position and freely transferred/traded to any RSK address. Intended to maintain parity with the US dollar, and can always be redeemed directly with the system: 1 ZUSD is always exchangeable for 1 USD worth of RBTC, minus a redemption fee.

_**Active Line of Credit:**_ an RSK address owns an “active Line of Credit” if there is a node in the `SortedTroves` list with ID equal to the address, and non-zero collateral is recorded on the Line of Credit struct for that address.

_**Closed Line of Credit:**_ a Line of Credit that was once active, but now has zero debt and zero collateral recorded on its struct, and there is no node in the `SortedTroves` list with ID equal to the owning address.

_**Active collateral:**_ the amount of RBTC collateral recorded on a Line of Credit’s struct

_**Active debt:**_ the amount of ZUSD debt recorded on a Line of Credit’s struct

_**Entire collateral:**_ the sum of a Line of Credit’s active collateral plus its pending collateral rewards accumulated from distributions

_**Entire debt:**_ the sum of a Line of Credit’s active debt plus its pending debt rewards accumulated from distributions

_**Individual collateralization ratio (ICR):**_ a Line of Credit's ICR is the ratio of the dollar value of its entire collateral at the current RBTC:USD price, to its entire debt

_**Nominal collateralization ratio (nominal ICR, NICR):**_ a Line of Credit's nominal ICR is its entire collateral (in RBTC) multiplied by 100e18 and divided by its entire debt.

_**Total active collateral:**_ the sum of active collateral over all Lines of Credit. Equal to the RBTC in the ActivePool.

_**Total active debt:**_ the sum of active debt over all Lines of Credit. Equal to the ZUSD in the ActivePool.

_**Total defaulted collateral:**_ the total RBTC collateral in the DefaultPool

_**Total defaulted debt:**_ the total ZUSD debt in the DefaultPool

_**Entire system collateral:**_ the sum of the collateral in the ActivePool and DefaultPool

_**Entire system debt:**_ the sum of the debt in the ActivePool and DefaultPool

_**Total collateralization ratio (TCR):**_ the ratio of the dollar value of the entire system collateral at the current RBTC:USD price, to the entire system debt

_**Critical collateralization ratio (CCR):**_ 150%. When the TCR is below the CCR, the system enters Recovery Mode.

_**Borrower:**_ an externally owned account or contract that locks collateral in a Line of Credit and issues ZUSD tokens to their own address. They “borrow” ZUSD against their RBTC collateral.

_**Depositor:**_ an externally owned account or contract that has assigned ZUSD to the Stability Pool, in order to earn returns from liquidations.

_**Redemption:**_ the act of swapping ZUSD with the system, in return for an equivalent value of RBTC. Any account with a ZUSD balance may redeem them, whether or not they are a borrower.

When ZUSD is redeemed for RBTC, the RBTC is always withdrawn from the lowest collateral Lines of Credit, in ascending order of their collateralization ratio. A redeemer can not selectively target Lines of Credit with which to swap ZUSD for RBTC.

_**Repayment:**_ when a borrower sends ZUSD to their own Line of Credit, reducing their debt, and increasing their collateralization ratio.

_**Retrieval:**_ when a borrower with an active Line of Credit withdraws some or all of their RBTC collateral from their own Line of Credit, either reducing their collateralization ratio, or closing their Line of Credit (if they have zero debt and withdraw all their RBTC)

_**Liquidation:**_ the act of force-closing an undercollateralized Line of Credit and redistributing its collateral and debt. When the Stability Pool is sufficiently large, the liquidated debt is offset with the Stability Pool, and the RBTC distributed to depositors. If the liquidated debt can not be offset with the Stability Pool, the system redistributes the liquidated collateral and debt directly to the active Lines of Credit with >110% collateralization ratio.

Liquidation functionality is permissionless and publicly available - anyone may liquidate an undercollateralized Line of Credit, or batch liquidate Lines of Credit in ascending order of collateralization ratio.

_**Collateral Surplus**_: The difference between the dollar value of a Line of Credit's RBTC collateral, and the dollar value of its ZUSD debt. In a full liquidation, this is the net gain earned by the recipients of the liquidation.

_**Offset:**_ cancellation of liquidated debt with ZUSD in the Stability Pool, and assignment of liquidated collateral to Stability Pool depositors, in proportion to their deposit.

_**Redistribution:**_ assignment of liquidated debt and collateral directly to active Lines of Credit, in proportion to their collateral.

_**Pure offset:**_  when a Line of Credit's debt is entirely cancelled with ZUSD in the Stability Pool, and all of its liquidated RBTC collateral is assigned to Stability Pool depositors.

_**Mixed offset and redistribution:**_  When the Stability Pool ZUSD only covers a fraction of the liquidated Line of Credit's debt. This fraction of debt is cancelled with ZUSD in the Stability Pool, and an equal fraction of the Line of Credit's collateral is assigned to depositors. The remaining collateral & debt is redistributed directly to active Lines of Credit.

_**Gas compensation:**_ A refund, in ZUSD and RBTC, automatically paid to the caller of a liquidation function, intended to at least cover the gas cost of the transaction. Designed to ensure that liquidators are not dissuaded by potentially high gas costs.