# Overview

## How we are using The Graph at Sovryn <a href="#how-we-are-using-the-graph-at-sovryn" id="how-we-are-using-the-graph-at-sovryn"></a>

The Graph solves the problems outlined above with a decentralized protocol that indexes and enables the performant and efficient querying of blockchain data. These APIs (indexed "subgraphs") can then be queried with a standard GraphQL API. Today, there is a hosted/self-hosted service as well as a decentralized protocol with the same capabilities. All of these are backed by the open source implementation of [Graph Node](https://github.com/graphprotocol/graph-node). This means that no matter which method is used, the queries used to access the data will never need to change.

When we first launched the Sovryn dapp, we built our own indexing server that listens to, processes and stores transactions, and then an API on top of this. However, this quickly became resource and time consuming to maintain and scale to the levels we require - for this reason we have migrated any of this logic from the original service to our new Sovryn subgraph.

As mentioned above, the Graph Protocol offers two different methods for capturing and exposing data:

1. Self Hosted/Hosted Subgraph
2. Decentralized Network Subgraph (hosted on the network itself)

Currently The Graph does not support Decentralized Network Subgraphs for the RSK network so we are using our own hosted subgraph - in the future when support for this is added, the transition can easily be made by pointing our dapp frontend to the Decentralized Network Subgraph, without needing to rewrite queries for additional optimizations. In addition, as the queries are written in the same GraphQL syntax regardless of what blockchain is used, this flexibility ensures a smooth migration with minimal changes if Sovryn were ever to branch out to other EVM compatible chains.

Below we have a high level view of how the information flows through the Graph Network to the Sovryn dapp:

<figure><img src="https://thegraph.com/docs/img/graph-dataflow.png" alt=""><figcaption></figcaption></figure>

The flow follows these steps:

1. Sovryn adds data to the RSK blockchain through a transaction on a smart contract.
2. The smart contract emits one or more events while processing the transaction.
3. Graph Node continually scans RSK for new blocks and the data they may contain for our subgraph.
4. Graph Node finds RSK events for your subgraph in these blocks and runs the mapping handlers you provided. The mapping is a WASM module that creates or updates the data entities that Graph Node stores in response to RSK events.
5. The decentralized application queries the Graph Node for data indexed from the blockchain, using the node's GraphQL endpoint. The Graph Node in turn translates the GraphQL queries into queries for its underlying data store in order to fetch this data, making use of the store's indexing capabilities. The decentralized application displays this data in a rich UI for end-users, which they use to issue new transactions on RSK. The cycle repeats.

## Benefits for Sovryn <a href="#benefits-for-sovryn" id="benefits-for-sovryn"></a>

* Anyone can query subgraphs. Out-of-the-box support for the flexible GraphQL API, which makes it easier for external developers to interact with data from the Sovryn ecosystem, with no reliance on Sovryn to provide data in a specific format
* Robust and reliable data
* Less developer effort required to maintain indexing server
* Faster iteration for data queries and debugging logic
* Better developer experience
* Greater transparency of how values are derived from on-chain data
* Easy transition to other chains if required

## Microservices <a href="#microservices" id="microservices"></a>

While The Graph is the ideal framework for indexing blockchain data, it does have certain limitations.

﻿One of these is that it can only react to blockchain data, but it cannot run scheduled jobs. Why is this an issue? Say, for example, you want to know what the 24 hour price change is of a token, and you want this data to be updated every 30 seconds. The Graph cannot do this kind of processing at scale. As an aside, the graph can respond to individual blocks instead of events, but this leads to much slower syncing times.

﻿Our solution has been to build a suite of microservices that use data from The Graph (and sometimes directly from the contracts) to handle these types of cases. Our suite of microservices and API endpoints use The Graph as their source of truth, add some additional logic/processing, and serve this data as an API in the format that the Sovryn dapp (or other products/parties) require.

﻿Our microservices are easily maintainable and extensible, and have paid off a large amount of the technical debt in our backend data services.

## Using The Graph <a href="#using-the-graph" id="using-the-graph"></a>

Users are encouraged to explore and use The Graph. Community members on various projects have built entire dashboards around queries to The Graph and similar blockchain databases.

To help users get started, we have provided [The Graph Technical Usage](https://docs.google.com/document/d/1VbOV7Muok-3AGr4de4xiYH77jZ\_q7bgcrl3xLeKi6qQ) documentation. We encourage you to ask questions on Discord and make suggestions on how to improve the documentation. Please share your insights with the community if you develop an interesting query.

The following repositories contain code and documentation regarding Sovryn subgraphs:

* Sovryn Subgraph: [https://github.com/DistributedCollective/Sovryn-subgraph](https://github.com/DistributedCollective/Sovryn-subgraph)
* Zero Subgraph: [https://github.com/DistributedCollective/Zero-subgraph](https://github.com/DistributedCollective/Zero-subgraph)
* AMM APY: [https://github.com/DistributedCollective/sovryn-amm-apy](https://github.com/DistributedCollective/sovryn-amm-apy)
* Graph Wrapper: [https://github.com/DistributedCollective/Sovryn-graph-wrapper](https://github.com/DistributedCollective/Sovryn-graph-wrapper)

## Some useful links and documentation:

Graph Protocol documentation: [https://thegraph.com/docs/en/](https://thegraph.com/docs/en/)

GraphQL documentation: [https://graphql.org/learn/queries/](https://graphql.org/learn/queries/)
