---
description: >-
  The liquidity mining contract pays ongoing rewards to the depositors of LP
  tokens.
---

# Liquidity Mining

The liquidity mining contract holds LP tokens and distributes rewards to the token owners for the duration the tokens stay in the contract. The actual value of the rewards depends on the configuration, which is updated weekly.

The accumulated rewards can be immediately liquid or deposited into a vesting contract, where an equal amount becomes liquid every four weeks over a period of 40 weeks. It is also possible for rewards to be partially liquid and partially vesting.

The source code resides in our [GitHub](https://github.com/DistributedCollective/Sovryn-smart-contracts/) repo.

This Builder Portal focuses on the most relevant functionality. A complete documentation of all functions can be found [here](https://github.com/DistributedCollective/Sovryn-smart-contracts/blob/development/docs/LiquidityMining.md).&#x20;
