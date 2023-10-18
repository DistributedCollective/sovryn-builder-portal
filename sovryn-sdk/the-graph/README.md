# The Graph

## What is The Graph? <a href="#what-is-the-graph" id="what-is-the-graph"></a>

_Text and images on this page are derived from the official documentation for The Graph, and have been modified to more closely describe how The Graph relates to the Sovryn ecosystem:_ [_https://thegraph.com/docs/en/about/_](https://thegraph.com/docs/en/about/)

The Graph is an indexing protocol for querying networks like Ethereum, BSC, or Rootstock. Anyone can build and publish open APIs, called subgraphs, making data easily accessible.

Projects with complex smart contracts like Sovryn store data on the blockchain, meaning that any data aggregation or larger amounts of historical data need to be processed, indexed and retrieved in a different way, as reading anything other than the current state directly from the blockchain is more complex.

For example, if we wanted to query for all Reward events corresponding to a certain address, and filter by one of its properties, we would not be able to get that information by interacting directly with the contract itself.

To get this data, you would have to process every single Reward event ever emitted, filter by the property, and then perform any additional manipulations to get the required dataset. Even for these types of relatively simple questions, it would take hours or even days for a decentralized application (dapp) running in a browser to get an answer.

You could also build out your own server, process the transactions there, save them to a database, and build an API endpoint on top of it all in order to query the data. However, this option is resource intensive, needs maintenance, presents a single point of failure, and breaks important security properties required for decentralization.

Blockchain properties like finality, chain reorganizations, or uncled blocks complicate this process further, and make it not just time consuming but conceptually hard to retrieve correct query results from blockchain data.
