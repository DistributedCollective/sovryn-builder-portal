# Advanced Usage

### Query parameters: Ordering

The Graph’s GraphQL server does not allow for aggregate functions (eg sum of… ), but it does allow you to pass in parameters to sort and filter results.

To sort results, you can use the orderBy and orderDirection properties like this:

```graphql
{
  users(orderBy: createdAtTimestamp, orderDirection: desc) {
    id
    createdAtTimestamp
  }
}

```

This will return all users ordered by the date they were created, in descending order. orderDirection will default to asc (ascending) if it is not included as a property.

You can only order by strings and numbers, not by nested entities, arrays or other data types.

### Query parameters: Pagination

You will notice that often a graph query will not return every single instance of entity. Our query on the previous slide returned \~100 users, not all \~20k users. This is because the payload and the load on the database to return that much data is too large and would make the graph too slow. Instead, the graph has pagination built in.

Using the parameters first and skip with orderBy, you can page through query results like this:

```graphql
{
  users(
    orderBy: createdAtTimestamp, 
    orderDirection: desc,
    first: 10,
    skip: 20
  ) {
    id
    createdAtTimestamp
  }
}
```

This query would return the third page of Users, with a page size of 10.

### Query parameters: Filtering

The Graph’s GraphQL implementation also allows for filtering within a query. You can do this by adding a where clause into your query, and the where clause can contain multiple properties:

<pre class="language-graphql"><code class="lang-graphql">{
<strong>  vestingContracts(
</strong>    where: {type: Strategic, currentBalance_gt: 100000}
  ) {
    id
    type
    currentBalance
  }
}

</code></pre>

This query returns all vesting contracts where the type is Strategic (a strategic investment round), and where the vesting contract has a balance greater than 100k SOV.

You can only filter for properties that exist on the entity itself, not inside nested entities. You can, however, filter for the ID of a nested entity:

```graphql
{
  swaps(where: {fromToken: "0x542fda317318ebf1d3deaf76e0b632741a7e677d"}) {
    fromToken {
      symbol
    }
    toToken {
      symbol
    }
  }
}

```

In this example, we can filter on fromToken ID, but cannot filter on fromToken symbol.

### Querying a single entity

Recall in the first example query, we queried the entity users. This returned a list of entities of type User. What if we just want a single user?

GraphQL query syntax uses plural and singular entity names to refer to multiple and single queries (the equivalent of find() vs findOne() if you are familiar with MongoDB).

To query a single entity, you will need to pass in an ID like this:

```graphql
{
  token(id: "0x542fda317318ebf1d3deaf76e0b632741a7e677d") {
    id
    symbol
    name
  }
}
```

### Querying a past block

A special property of The Graph's implementation of GraphQL is that it allows you to query the state of your subgraph at any past block, similar to how you can query the state of the blockchain at any point in the past.

To do this, we just need to pass in a block number or block hash as a query parameter, and the subgraph will return the results of the query as they were at that block.

For example, let’s look at the dollar price of WRBTC at two different blocks. The following query returns the lastPriceUsd of the WRBTC token at block 3000000 (2021/01/05 06:17:50):

```graphql
{
  token(id: "0x542fda317318ebf1d3deaf76e0b632741a7e677d",
   block: {number: 3000000}
  ) {
    id
    symbol
    name
    lastPriceUsd
  }
}
```

The result we see is:

<figure><img src="https://lh5.googleusercontent.com/lj1FguVNBLIUid1x49Y148IXs9WWQnsSwrQnmq-NtO3kNiLQt-YjFPAO0vQxCkTZYZ_Mo9-2O5mzIYogXFlBjjRwCCINeo1smSO63PQvEzFRledc-kKi_3Sx7gn81fT-xw1mxrKgV-L9Yf7XxfrEzwA" alt=""><figcaption></figcaption></figure>

We could then compare this to the WRBTC price at another block, for example block 4000000 (2022/01/12 20:40:28):

```graphql
{
  token(id: "0x542fda317318ebf1d3deaf76e0b632741a7e677d",
   block: {number: 4000000}
  ) {
    id
    symbol
    name
    lastPriceUsd
  }
}
```

This is the result:

<figure><img src="https://lh6.googleusercontent.com/mWvP4342_NAPGZotZyzdem4NenHWcn73i8g2nqLe5xfyBRCDgz8stBCnNgAO5TPW_U8vy2xY7PAiP0OMq0seoo7w6uI77oowQz4YEg1Ilx4UybptEHnTl2VC3e9FWvtV-ZLi9H-_1TksB-b8xg7RFOE" alt=""><figcaption></figcaption></figure>

From this, we can see the price change between these two points for this token.
