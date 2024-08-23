---
description: >-
  Bitocracy is the heart the Sovryn's protocol. It is the mechanism that enables the governance of its code and the incentive to maintain the system through the distribution of dividends.
---

# BITOCRACY

<figure><img src="../../.gitbook/assets/bitocracy.png" alt=""><figcaption><p>Sovryn's Bitocracy</p></figcaption></figure>

Mynt is a stablecoin aggregator based on [BabelFish](https://github.com/BabelFishProtocol/babelfish-phase-1). In contrast to BabelFish, which supports stablecoins with many different types of collateral backing, the stablecoins that Mynt supports are exclusively backed by BTC. Users can use Mynt to convert between supported "bAsset" stablecoins and the Mynt "mAsset" stablecoin at a 1:1 ratio. The Mynt mAsset stablecoin is the Sovryn Dollar (DLLR).

By aggregating multiple different BTC-backed stablecoins, each with their own issuance and stability mechanisms, DLLR has the potential to be more resilient, stable, and scalable than any of the underlying stablecoins on their own.

Masset contract is the main element in the protocol.

## 1. Staking

Basic assets that are validated in the Basket Manager storage, and are aggregated to be converted 1:1 into the meta-asste token, the Sovryn Dollar or "DLLR".

The most general function to mint DLLR is `masset.mintTo`.

## 2. Governor

The meta-asset token can be converted back to any of its baking assets; currently the "Dollar-on-Chain" from the Money on Chain protocol, and the "ZUSD" from Sovryn's ZERO protocol, through a "redeem" funtion.

The most general function to redeem DLLR is `masset.redeemTo`.

## 3. Fee Sharing Collector

Mynt has an additional feature that allows users to convert DLLR directly into rBTC (native RSK asset). The conversion from ZUSD to rBTC is carried on by the ZERO protocol, but in order to do the converion via Money-on-Chain protocol (MOC), we implemented an integrator.

The most general function to redeem DLLR into rBTC via Money on Chain is `MocIntegration.getDocFromDllrAndRedeemRbtcWithPermit2`.

## 4. Vesting

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