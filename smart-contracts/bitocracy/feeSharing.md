---
description: >-
  The FeeSharingCollector contract. This contract withdraws fees to be paid to SOV Stakers from the protocol. Stakers call withdraw() to get their share of the fees.
---

# FEE SHARING COLLECTOR CONTRACT

Staking is not only granting voting rights, but also access to fee sharing according to the own voting power in relation to the total. Whenever somebody decides to collect the fees from the protocol, they get transferred to this contract and converted when possible into rBTC. The fee sharing contract is set as `feesController` of the protocol contract. This allows the fee sharing contract to withdraw the fees. The fee sharing proxy holds the pool tokens and keeps track of which user owns how many tokens.  

A similar dynamics happens with the AMM network of converters, and with the ZERO protocol. Also there was a program earning rewards from the "MYNT Bootstrap" protocol. So, the fee sharing contract can distribute `rBTC`, `ZUSD`, `SOV` and for some old stakers, some `MYNT` yet.  

In order to know how many tokens a user owns, the fee sharing proxy needs to know the user’s weighted stake in relation to the total weighted stake (aka total voting power). Because both values are subject to change, they may be different on each fee withdrawal. To be able to calculate a user’s share of tokens when he wants to withdraw, we need checkpoints in time.  

This contract is intended to be set as the protocol fee collector. Anybody can invoke the withdrawFees function which uses the `protocol.withdrawFees` function to obtain available fees from operations on a certain token. These fees are deposited in the corresponding loanPool. When a user calls the withdraw function, the contract transfers the fee sharing rewards in proportion to the user’s weighted stake since the last withdrawal.  

## EXTRACTING REWARDS FROM CORE PROTOCOL, AMM AND ZERO

Essentially, all fees distributed from the ZERO protocol are sent to the fee sharing contract each time a set of transaction of such protocol are performed. This is intimately related to the history of the Sovryn protocol: FeeSharingCollector was implemented after the AMM and the core protocol, so the fee distribution code was already set on those protocols, and we need a lump transfer from the fees collected from those protocols to the fee sharing;  while in the ZERO protocol, this setting was done in the process of implementing it on our ecosystem.  

The extraction of fees from the core protocol is done by `feeSharingCollector.withdrawFees` function:  

```solidity
function withdrawFees(address[] calldata _tokens) external
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_tokens</td><td>address[]</td><td>Array address of the underlying tokens of each lending pool of the core protocol<br><br>This involves lendingFee + tradingFee + borrowingFee</td></tr></tbody></table>

he extraction of fees from the AMM network protocol is done by `feeSharingCollector.withdrawFeesAMM` function:

```solidity
function withdrawFeesAMM(address[] memory _converters) public
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_converters</td><td>address[]</td><td>Array addresses of the AMM converters<br> -No of the assets-</td></tr></tbody></table>

While a cronjob execute the former transactions once per week, anybody can execute them.  

## CLAIMING REWARDS

Currently there are two functions a user can execute in order to retrieve their rewards from Sovryn's protocol: 

```solidity
function withdraw(
        address _token,
        uint32 _maxCheckpoints,
        address _receiver
    ) public nonReentrant 
```

This function will withdraw accumulated fees per asset to the message sender. This method is suited for users that regularly withdraw their fees. When time has passed since the last withdrawal, gas fees can easily exceed the block gas limit of RSK network.

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_token</td><td>address</td><td>Asset reward address; for rBTC, we use the dummy address:<br><br>0xeabd29be3c3187500df86a2613c6470e12f2d77d</td></tr><tr><td>_maxCheckpoints</td><td>uint32</td><td>Maximum number of checkpoints to be processed. Must be positive value, and recommended to be less than 100.<tr><td>_receiver</td><td>address</td><td>The receiver of tokens or msg.sender<br>.</tbody></table>  

However, when to many assets per checkpoint are left to claim, the `withdraw` function becomes unfeasible, then we can use the following:

```solidity
function claimAllCollectedFees(
        address[] calldata _nonRbtcTokensRegularWithdraw,
        address[] calldata _rbtcTokensRegularWithdraw,
        TokenWithSkippedCheckpointsWithdraw[] calldata _tokensWithSkippedCheckpoints,
        uint32 _maxCheckpoints,
        address _receiver
    ) external nonReentrant
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>_nonRbtcTokensRegularWithdraw</td><td>address[]</td><td>array of non-rbtc token address with no skipped checkpoints that will be withdrawn</td></tr><tr><td>_rbtcTokensRegularWithdraw</td><td>address[]</td><td>array of rbtc token address with no skipped checkpoints that will be withdrawn<tr><td>_tokensWithSkippedCheckpoints</td><td>TokenWithSkippedCheckpointsWithdraw[]<br><br>an array of parameters of struct type:<br>TokenWithSkippedCheckpointsWithdraw</td><td>array of rbtc & non-rbtc TokenWithSkippedCheckpointsWithdraw struct, which has skipped checkpoints that will be withdrawn<tr><td>_maxCheckpoints</td><td>uint32</td><td>Maximum number of checkpoints to be processed. Must be positive value, and recommended to be less than 100.<tr><td>_receiver</td><td>address</td><td>The receiver of tokens or msg.sender</tbody></table>

The former function is complex to execute, so in order to get more details on how to implement it in an interface, the user can check the [unit tests](https://github.com/DistributedCollective/Sovryn-smart-contracts/blob/development/tests/FeeSharingCollectorTest.js#L450) of this function.

