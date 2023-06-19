# Usage

Example on how to use simple graph request

1. Go to the subgraph explorer: [https://subgraph.sovryn.app/subgraphs/name/DistributedCollective/sovryn-subgraph/graphql](https://subgraph.sovryn.app/subgraphs/name/DistributedCollective/sovryn-subgraph/graphql)

<figure><img src="https://lh5.googleusercontent.com/dEwaTeD3qtB2rxGpB-Mr6bihq7Kd6O5zw76qRHfaGbnyYtfUUnO17H0M53MxBDDZxMlrDVhjhSZDIb3RosdSN5w3HI6CQqbj3-Gzu4byvsqkM3WFo4J8sz6uVWk4pSywmpE94Muu9aLwfNrHlySoBtc" alt=""><figcaption></figcaption></figure>

2. Decide what you want to query. For this example, we want to see how much SOV each user has voluntarily staked on the Staking contract.
3. Use the schema explorer to find the correct entities to query:

```graphql
{
  users {
    id
    stakeHistory {
      totalRemaining
    }
  }
}
```

4. Write your query in the left-hand column of the explorer. Read the GraphQL or Graph Protocol documentation for help with the graphQL syntax
5. Run your query. Take a look at the results:

<img src="https://lh6.googleusercontent.com/bXNrOmQTNCnrmIFKxqlBl83uDN9ZfDdWhPKfxPfFDeI4MfpoAtXtAdNhT_StCdb9dt5ZwaSj75ynblC_5GS3D60ze1UVq_P1N_LxpEp0ZLQZb7kzZNbmpF1RfUwDPRrYy_VHRLzh6RQLEFXfu1Dbog" alt="" data-size="original">

6. Edit query if needed. This result probably isn’t what you were looking for - here we are listing all users in alphabetical order by wallet address, even if they do not have any voluntarily staked SOV. We can change the query to this:

```graphql
{
    userStakeHistories(orderBy: totalRemaining, orderDirection: desc) {
        id
        totalRemaining
      }
}
```

This query returns results only for users who have voluntarily staked SOV, and orders the results with the largest current SOV staked amount first.
