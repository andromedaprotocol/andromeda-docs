---
description: Queries using our GraphQL server
---

# Gql

### Available Commands

| Command                     | Description                                                              |
| --------------------------- | ------------------------------------------------------------------------ |
| [**app**](gql.md#app)       | Queries details about an app.                                            |
| [**assets**](gql.md#assets) | Queries details about your deployed apps and ADOs for the current chain. |
| [**url**](gql.md#url)       | Fetches the GraphQL url used.                                            |

## App

Queries details about an app.

#### Usage

```
gql app <contract address>
```

#### Example

```
gql app andr1vtsclrxc0vk8qx3xqj4nm9uczqxc5rh3w4ey2ha9zz0cpv8lrncsp436gs
```

#### Response

The response includes the owner address, app name, and components of the app.

```
- Searching the Cosmos...
Owner: andr1vcmlq0snkv8tjccxq8r86aqd3l67xlwnqgma7q
App Name: Crowdfunding

Components
Crowdfund   crowdfund andr10y4jzxavk0uw2usy7ezt4dq5h0k64na8c9yz3rq3dk50v7j8mezsq8cnce
Splitter    splitter  andr1nywjc2mfzl2fud8epzrllj2f2xlpnk2tfpnf49qh02z7c97gv2dqw3qvdn
Tokens      cw721     andr1zkjmt8x78w5de0pysh6yrdd3alxf7dp8uv80ey2yadjxsaywy3vquj76lx
Yield-Vault vault     andr1k528kg8h3q56j5yazshv39fafmhjzl4540u7w36g6q2amgyrpwpsf50ury
```

## Assets

Queries details about your deployed apps and ADOs for the current chain.

#### Usage

```
gql assets
```

{% hint style="warning" %}
The result is the same as running "ado list"
{% endhint %}

#### Example

```
? guides@galileo-3> gql assets
⠂ Searching the Cosmos...
Address                                                         ADO Type App Contract                                                   
andr1dtaqwlmzlk3jku5un6h6rfunttmwsqnfz7evvdf4pwr0wypsl68qa30vxv cw20                                                                    
andr138gmtsqwcz0z09ja6y4cs3uyestx85440xcuwsght70jvmc9v92s9lwvj0 app                                                                     
andr1qdgvugdnscwnj8lc96q666000gyjv434kn9zl9ey3dph6p0cunuszv3dwf cw721    andr138gmtsqwcz0z09ja6y4cs3uyestx85440xcuwsght70jvmc9v92s9lwvj0
? guides@galileo-3> () 
```

#### Flags

| Flag       | Description                                              | Usage        |
| ---------- | -------------------------------------------------------- | ------------ |
| **type**   | Filter assets by ADO type.                               | --type cw721 |
| **limit**  | The number of assets to return. Defaults to 10.          | --limit 5    |
| **offset** | Number of results to skip from the start. Defaults to 0. | --offset 3   |
