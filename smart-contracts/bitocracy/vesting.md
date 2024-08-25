---
description: >-
  Staking contract allows SOV owners to put their assets in a locked position, commiTted until a given date. In exchange the user receives rights in the voting system (the `GovernorAlpha` contract) and in the fee sharing contract rewarding them with a cut of the fees collected by the Sovryn protocol. This whole system - staking, governance and fee sharing - is known as "Bitocracy".
---

# VESTING CONTRACTS

<figure><img src="../../.gitbook/assets/vesting_diagram.png" alt=""><figcaption><p>Vesting Contracts Protocol</p></figcaption></figure>

## VESTING CONTRACTS PURPOSE

Staking contract is an upgradeable contract deployed with a specific strategy to save the most amount of bytecode per module. As shown in the figure, the logics of the `Staking` contract is distributed in seven modules as described below:


## VESTING CREATION

In order to open a staking position, the user must own a balance of valid assets:`SOV` so far.

The user must first execute the proper `approve` function to the ERC20 contract of `SOV`.

After such transaction the user can address the execution of `stake` to the `Staking` contract. The `stake` function implies the internal execution of several `delegatecall`s to the `StakingModuleProxy` and several of logic modules.

```solidity
function createVestingAddr(
        address _tokenOwner,
        uint256 _amount,
        uint256 _cliff,
        uint256 _duration,
        uint256 _vestingCreationType
    ) public onlyAuthorized
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>amount</td><td>uint96</td><td>The number of SOV tokens to stake</td></tr><tr><td>until</td><td>uint256</td><td>Timestamp in seconds<br> -blockchain format-<br> indicating the date until which to stake</td></tr><tr><td>stakeFor</td><td>address</td><td>The address to stake the tokens for or zero address if staking for oneself</td></tr><tr><td>delegatee</td><td>address</td><td>The address of the delegatee or zero address if there is none.</td></tr><tr><td>stakeFor</td><td>address</td><td>The address to stake the tokens for or zero address if staking for oneself</td></tr></tbody></table>

## VESTING FUNDING

If a user had or still have a staking position can perform call the function `withdraw` to the `Staking` contract. Early unstaking will be punished. The current destin of punished SOV is the zero address (they are burned).

```solidity
function stakeTokens(address _vesting, uint256 _amount) external onlyAuthorized 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>amount</td><td>uint96</td><td>The number of SOV tokens to stake</td></tr><tr><td>until</td><td>uint256</td><td>Timestamp in seconds<br> -blockchain format-<br> indicating the date until which to stake<br><br>If until is not a valid "unlock" date, the next lock date after until is used.</td></tr></tbody></table>

## VESTING WITHDRAWALS

If a user had or still have a staking position can perform call the function `withdraw` to the `Staking` contract. Early unstaking will be punished. The current destin of punished SOV is the zero address (they are burned).

```solidity
function withdrawTokens(address receiver) public onlyOwners 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>amount</td><td>uint96</td><td>The number of SOV tokens to stake</td></tr></tbody></table>
