---
description: List of Kernel Addresses for each of the chains integrated with Andromeda.
---

# Deployed Contracts

Testnet

To get the list of kernel addresses for testnet chains, we can use the chainConfigs query in the GQL playground.

{% embed url="https://api.andromedaprotocol.io/graphql/testnet" %}

Run the following query:

```graphql
query CODEGEN_GENERATED_CHAINCONFIGS_ALLCONFIGS {
  chainConfigs {
    allConfigs {
      chainId
      name
      kernelAddress
    }
  }
}
```

**Example Result:**

<figure><img src="../.gitbook/assets/Screenshot 2024-07-23 at 6.08.42 PM.png" alt=""><figcaption></figcaption></figure>

Mainnet

We can use the same query to get the list of kernel addresses for mainnet deployment:

{% embed url="https://api.andromedaprotocol.io/graphql/mainnet" %}
