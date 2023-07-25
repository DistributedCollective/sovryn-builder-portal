---
description: >-
  The liquidity mining contract pays ongoing rewards to the depositors of LP
  tokens.
---

# Liquidity Mining

The liquidity mining contract holds LP tokens and distributes rewards to the token owners for the duration the tokens stay in the contract. The actual height of the rewards depend on the configuration, which is updated weekly.

The accumulated rewards can be immediately liquid or deposited into a vesting contract, where are an equal amount becomes liquid every four weeks over a period of 40 weeks. It is also possible for rewards to be partially liquid and partially vesting.

The source code resides in our [github](https://github.com/DistributedCollective/Sovryn-smart-contracts/).

This builder portal focuses on the most relevant functionality. A complete documentation of all functions can be found [here](https://github.com/DistributedCollective/Sovryn-smart-contracts/blob/development/docs/LiquidityMining.md).&#x20;
