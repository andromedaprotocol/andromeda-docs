# GraphQL Queries

### GraphQL URIs

### Mainnet

{% embed url="https://api.andromedaprotocol.io/graphql/mainnet" %}

### Testnet

{% embed url="https://api.andromedaprotocol.io/graphql/testnet" %}

### Examples

You can find all the available queries in the Docs section on the right of the playground:

{% hint style="warning" %}
If unsure about one of the query fields, you can find more info about it in the ADOs documentation page in the [smart contract docs](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/introduction-to-ados).
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-07-17 at 10.06.32 PM.png" alt=""><figcaption></figcaption></figure>

Here is an example of querying a CW20 ADO for information:

```graphql
query CW20($contractAddress: String!) {
  ADO {
    cw20(address: $contractAddress) {
      address
      marketingInfo {
        description
        logo
        marketing
        project
      }
      minter {
        cap
        minter
      }
    }
  }
}

```

#### Result

{% hint style="warning" %}
Dont forget the set a value for the  Query variables.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-07-17 at 10.20.25 PM.png" alt=""><figcaption></figcaption></figure>

You can also try the query by running the following in your terminal:

{% code overflow="wrap" fullWidth="false" %}
```bash
curl 'https://api.andromedaprotocol.io/graphql/testnet' \
-H 'Content-Type: application/json' \
-H 'Accept: application/json' \
-H 'Connection: keep-alive' \
-H 'DNT: 1' \
-H 'Origin: https://api.andromedaprotocol.io' \
--data-binary '{"query":"query CW20($contractAddress: String!) {\n  ADO {\n    cw20(address: $contractAddress) {\n      address\n      marketingInfo {\n        description\n        logo\n        marketing\n        project\n      }\n      minter {\n        cap\n        minter\n      }\n    }\n  }\n}\n","variables":{"contractAddress":"andr10n3zrq9z7e5qmqhryazxmgjx7tkjumyudtv34j6vzlpst5r8g9xsex8zav"}}' --compressed
```
{% endcode %}

Query chain Ids for all chains:

```graphql
query chainConfigs{
  chainConfigs{
    allConfigs{
      chainId
    }
  }
}
```

Result

<figure><img src="../.gitbook/assets/Screenshot 2024-07-17 at 10.26.54 PM.png" alt=""><figcaption></figcaption></figure>

{% code overflow="wrap" %}
```bash
curl 'https://api.andromedaprotocol.io/graphql/testnet' \
-H 'Accept-Encoding: gzip, deflate' \
-H 'Content-Type: application/json' \
-H 'Accept: application/json' \
-H 'Connection: keep-alive' \
-H 'DNT: 1' \
-H 'Origin: https://api.andromedaprotocol.io' \
--data-binary '{"query":"query chainConfigs{\n  chainConfigs{\n    allConfigs{\n      chainId\n    }\n  }\n}","variables":{"contractAddress":"andr10n3zrq9z7e5qmqhryazxmgjx7tkjumyudtv34j6vzlpst5r8g9xsex8zav"}}' --compressed
```
{% endcode %}

You can find more GQL query formats [here](https://github.com/andromedaprotocol/andromeda.js/blob/development/packages/gql/schema/master-schema.graphql).
