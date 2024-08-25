---
description: >-
  The GovernorAlpha contract is an adapted clone of Compound’s governance model.
---

# GOVERNOR ALPHA CONTRACT

The `GovernorAlpha` contract has two instances:

* The `GovernorOwner`: Which is the one authorized to perform changes in the code of the Sovryn's protocol.

* The `GovernorAdmin`: Which is the one authorized to perform settings in parameters of the code of the Sovryn's protocol, and make movement of funds.


## MAKING A PROPOSAL

Stakers can make (executable) proposals if they possess enough voting power, currently above the 1% of the total voting power.

The execution of the `GovernorAlpha.propose` returns the number ID of the proposal.

```solidity
function propose(
        address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    ) public returns (uint256) 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>targets</td><td>address[]</td><td>Array of contract addresses to perform proposal execution</td></tr><tr><td>values</td><td>uint256[]</td><td>Array of rBTC amounts to send on proposal execution</td></tr><tr><td>signatures</td><td>string[]</td><td>Array of function signatures to call on proposal execution</td></tr><tr><td>calldatas</td><td>bytes[]</td><td>Array of payloads for the calls on proposal execution</td></tr><tr><td>description</td><td>string</td><td>Text describing the purpose of the proposal</td></tr></tbody></table>

## VOTING A PROPOSAL

Once a proposal is successfully deployed, it has a window to be voted of `2880 blocks` in RSK blockchain, which is around 24 hours. Only stakers will have voting power. The execution of the former function does not revert if you are not a staker user: it just add zero voting weight to the proposal, and spends the needed gas. 

```solidity
castVote(uint256 proposalId, bool support) public
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>proposalId</td><td>uint256</td><td>Proposal index to access the list proposals[] from storage</td></tr><tr><td>support</td><td>bool</td><td>Vote value, yes or no: whether you support or not the proposal</tbody></table>

## QUEUEING A PROPOSAL

If a proposal earns enough voting power in favor, the proposal must be put in a queue of 48 hours: this allow users that do not agree with the results to withdraw their funds from Sovryn protocol. Anybody can put in que an approved proposal.

```solidity
function queue(uint256 proposalId) public 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>proposalId</td><td>uint256</td><td>Proposal index to access the list proposals[] from storage</td></tr></tbody></table>

## EXECUTING A PROPOSAL

After the minimum queue time windows is done, anybody can execute an approved proposal.

```solidity
function execute(uint256 proposalId) public payable 
```

**Arguments**

<table><thead><tr><th width="200.33333333333334">Name</th><th width="165">Type</th><th>Description</th></tr></thead><tbody><tr><td>proposalId</td><td>uint256</td><td>Proposal index to access the list proposals[] from storage</td></tr></tbody></table>

