# Graph Usage

Where possible we:

* Don't perform calculations on frontend - use data directly from subgraphs instead
* Use GraphQL client (Apollo) for performing queries - provides auto generated Typescript definitions and client side caching of responses
* Reuse queries - aids in caching of responses

See [here ](../other-products/the-graph/overview.md)for more information about how we use The Graph in sovryn.app.
