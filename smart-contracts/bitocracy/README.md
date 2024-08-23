---
description: >-
  Bitocracy is the heart the Sovryn's protocol. It is the mechanism that enables the governance of its code and the incentive to maintain the system through the distribution of dividends.
---

# BITOCRACY

<figure><img src="../../.gitbook/assets/bitocracy.png" alt=""><figcaption><p>Sovryn's Bitocracy</p></figcaption></figure>

Bitocracy is based on three basic modules: Staking, Governance and Fee Sharing modules. When a user acquires some SOV asset they are able to put it in a stake position. The position value depends on the time the asset is committed, and the relation between time, asset amount and value is close to - when not exactly - a quadratic function.

**Staking**: For this, the user needs to `approve` the handling of the SOV asset to the Staking contract, and then call `Staking.stake`. This action makes the user into a "staker".

**Governor**: Having a staking position allows the user to vote for a proposal (`castVote`), and under certain conditions - having a net 1% of current total voting power in all their staking positions - to make a proposal (`propose`). If a proposal reaches enough votes in favor - the status of the proposal is "succeeded" - anybody can call the function `Governor.queue`, which prepares the proposal to be executed; and after a `delay` time window - which is 48 hours for the `GovernorOwner` and `TimelockOwner`, and 24 hours for `GovernorAdmin` and `TimelockAdmin` - anybody can call the function `Governor.execute`.

The difference between the `GovernorOwner` instance of `GovernorAlpha` - or, just `Governor` - and `GovernorAdmin` is the capabilities to execute the actions of a proposal. The "owner" version is destined to any change of code on the whole Sovryn protocol, while the "admin" version is destined to the manage of funds.

**Timelock**: This contract filters if a set of actions coming from a proposal have the enough clearance to be executed. Only the `Governor` can call `Timelock.executeTransaction`, and only `Timelock` has the authority to perform changes on Sovryn's code and manage some funds from certain pools.

**Fee Sharing**: The contract `FeeSharingCollector` rewards with revenues and profits coming from the fees collected in the Sovrn protocol to all stakers. As the `Governor` contract, the `FeeSharingCollector` makes internal calls to the `Staking` contract to be able to estimate the proper amount of funds a staker, based on a parameter from which the proper share of fees is calculated. In the case of the `Governor`, it allows to estimate the voting power, by the moment of casting a vote.

**Vesting**: Not shown in the former diagram, the `Vesting` sub-set of the staking protocol, is a method to reward users, Sovryn's core team and other contributors that participate in several programs that community regards valuable. But instead of rewarding these people with liquid SOV assets, the SOV is delivered under a "vesting" schedule.

## 1. [Staking](staking.md)

Basic assets that are validated in the Basket Manager storage, and are aggregated to be converted 1:1 into the meta-asste token, the Sovryn Dollar or "DLLR".

The most general function to mint DLLR is `masset.mintTo`.

## 2. [Governor](governor.md)

The meta-asset token can be converted back to any of its baking assets; currently the "Dollar-on-Chain" from the Money on Chain protocol, and the "ZUSD" from Sovryn's ZERO protocol, through a "redeem" funtion.

The most general function to redeem DLLR is `masset.redeemTo`.

## 3. [Fee Sharing Collector](feeSharing.md)

Mynt has an additional feature that allows users to convert DLLR directly into rBTC (native RSK asset). The conversion from ZUSD to rBTC is carried on by the ZERO protocol, but in order to do the converion via Money-on-Chain protocol (MOC), we implemented an integrator.

The most general function to redeem DLLR into rBTC via Money on Chain is `MocIntegration.getDocFromDllrAndRedeemRbtcWithPermit2`.

## 4. [Vesting](vesting.md)

Fees are currently disabled. But if they are ever enabled, here is how they will work:

##### **`- mint`**( take bAssets, mint mAssets in exchange )
&NewLine;
##### **- fees**
-   substract fee from calculated mAsset mint amount
-   fees does not impact the amount of bAssets, all of them are transfered to pool 


##### **`- redeem`**( burn mAssets, transfer bAssets from pool in exchange  )
&NewLine;
##### **- fees**
-   transfer calculated amount before all conversions
-   substract from massets to burn and bassets to transfer