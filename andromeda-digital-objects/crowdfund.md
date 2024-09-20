# Crowdfund

## Introduction

The **Crowdfund** ADO is a smart contract that allows users to start a campaign and raise funds for their projects/goal by selling NFTs.

Each campaign can contain multiple tiers for different levels of support. For example you could have a bronze, silver, and gold tier for your crowdfund each having a specified price. All NFTs of the same tier share the same metadata.&#x20;

When the campaign is over, the user can claim their NFT that corresponds to the tier they have purchased. In case the crowdfund does not reach its set goal, users would get refunds for their purchases.  The sale can be performed using either native tokens or CW20 tokens depending on what the owner wants.

{% hint style="info" %}
The NFTs are minted at the end of the sale. Each NFT will take the metadata assigned to its tier.&#x20;
{% endhint %}

**Ado\_type**: crowdfund

**Version: 2.0.2-beta.1**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub campaign_config: CampaignConfig,
    pub tiers: Vec<Tier>,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "campaign_config": {
    "title": "Campaign Title",
    "description": "This is a short description of the campaign.",
    "banner": "https://example.com/banner.jpg",
    "url": "https://example.com",
    "token_address": "andr1...",
    "denom": {
      "native_token": "uandr"  
    },
    "withdrawal_recipient": {
      "address": "andr1..."  
    },
    "soft_cap": "100",  
    "hard_cap": "500"
  },
  "tiers": [
    {
      "level": "1",
      "label": "Bronze",
      "price": "1000",
      "limit": null,  
      "sold_amount": "0",
      "metadata": {
        "extension": {
          "publisher": "Publisher Name"
        }
      }
    },
    {
      "level": "2",
      "label": "Silver",
      "price": "2000",
      "limit": "500",
      "metadata": {
        "token_uri": "https://example.com/tier2.json",  
        "extension": {
          "publisher": "Publisher Name"
        }
      }
    }
  ],
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="259.3333333333333">Name</th><th width="212.3589484327604">Type</th><th>Desctription</th></tr></thead><tbody><tr><td><code>campaign_confing</code></td><td><a href="crowdfund.md#campaignconfig">CampaignConfig</a></td><td>The configurations for the campaign.</td></tr><tr><td><code>tiers</code></td><td>Vec&#x3C;<a href="crowdfund.md#tier">Tier</a>></td><td>Defines the tiers available for purchase in the campaign.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

#### CampaignConfig

Struct containing all of the campaign's configurations.

```rust
pub struct CampaignConfig {
    pub title: String,
    pub description: String,
    pub banner: String,
    pub url: String,
    pub token_address: AndrAddr,
    pub denom: Asset,
    pub withdrawal_recipient: Recipient,
    pub soft_cap: Option<Uint128>,
    pub hard_cap: Option<Uint128>,
}
```

<table><thead><tr><th width="259.3333333333333">Name</th><th width="212.3589484327604">Type</th><th>Desctription</th></tr></thead><tbody><tr><td><code>title</code></td><td>String</td><td>The title of the campaign. Maximum length for the title is 64 characters.</td></tr><tr><td><code>description</code></td><td>String</td><td>A description about the campaign.</td></tr><tr><td><code>banner</code></td><td>String</td><td>URL pointing to the campaign's banner image.</td></tr><tr><td><code>url</code></td><td>String</td><td>URL pointing to the campaign's website.</td></tr><tr><td><code>token_address</code></td><td><a href="https://docs.andromedaprotocol.io/andromeda/platform-and-framework/common-types#andraddr">AndrAddr</a></td><td>The address of the CW721 contract whose NFTs will be sold in the campaign.</td></tr><tr><td><code>denom</code></td><td><a href="../platform-and-framework/common-types.md#asset">Asset</a></td><td>The type of funds accepted to buy the NFT tiers in the campaign.</td></tr><tr><td><code>withdrawal_recipient</code></td><td>Recipient</td><td>The address to receive the funds from the campaign proceeds.</td></tr><tr><td><code>soft_cap</code></td><td>Option&#x3C;Uint128></td><td>An optional minimum amount of funds to be raised by the campaign for it to be considered successfull. Failing to meet this cap will result in refunds for the users.</td></tr><tr><td><code>hard_cap</code></td><td>Option&#x3C;Uint128></td><td>An optional maximum amount of funds that can be raised by the campaign.</td></tr></tbody></table>

#### Tier

Struct that defines a tier for the campaign.

```rust
pub struct Tier {
    pub level: Uint64,
    pub label: String,
    pub price: Uint128,
    pub limit: Option<Uint128>,
    pub metadata: TierMetaData,
}
```

| Name          | Type                                      | Description                                                                               |
| ------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------- |
| `level`       | Uint64                                    | The tier level. For example 1 for the highest tier, 2 for seconds highest etc...          |
| `label`       | String                                    | A label to assign to the tier.                                                            |
| `price`       | Uint128                                   | The amount of denom needed to purchase this tier.                                         |
| `limit`       | Option\<Uint128>                          | A limit to the number of NFTs available for this tier. Defaults to none if not specified. |
| `sold_amount` | Uint128                                   | Field used to for queries to get the amount sold for each tier. Should be set to 0.       |
| `metadata`    | [TierMetadata](crowdfund.md#tiermetadata) | The metadata for this tier.                                                               |

#### TierMetadata

```rust
pub struct TierMetaData {
    pub token_uri: Option<String>,
    pub extension: TokenExtension,
}
```

| Name        | Type                                          | Description                                                                                                                                                 |
| ----------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token_uri` | Option\<String>                               | URI pointing to the NFT metadata. You can read more about the NFT token URI and metadata standards [here](https://docs.opensea.io/docs/metadata-standards). |
| `extension` | [TokenExtension](crowdfund.md#tokenextension) | Any custom extension used by this contract. Here we use [TokenExtension](crowdfund.md#tokenextension).                                                      |

### TokenExtension

Extension that can be added to an NFT when minting.

```rust
pub struct TokenExtension {
   pub publisher: String,
}
```

<table><thead><tr><th width="248.55224634023222">Name</th><th width="260.3333333333333">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>publisher</code></td><td>String</td><td>The entity to assign as the publisher of the NFT e.g. "Andromeda" or "Bob". (Immutable).</td></tr></tbody></table>

## ExecuteMsg

### AddTier

Adds a new tier to the campaign.

{% hint style="warning" %}
Can only be used if the campaign has not started yet.

Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddTier { tier: Tier },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_tier":{
      "level": "3",
      "label": "Gold",
      "price": "5000",
      "limit": "500",
      "metadata": {
        "token_uri": "https://example.com/tier2.json",  
        "extension": {
          "publisher": "Publisher Name"
        }
      }
    }
  }
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="225">Type </th><th>Description</th></tr></thead><tbody><tr><td><code>tier</code></td><td><a href="crowdfund.md#tier">Tier</a></td><td>The tier configurations.</td></tr></tbody></table>

### UpdateTier

Updates the configurations of a previously set tier.

{% hint style="warning" %}
Can only be used if the campaign has not started yet.

Updates the tier with the for the specified level.

Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateTier { tier: Tier },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_tier":{
      "level": "3",
      "label": "Gold",
      "price": "5000",
      "limit": "500",
      "metadata": {
        "token_uri": "https://example.com/tier2.json",  
        "extension": {
          "publisher": "Publisher Name"
        }
      }
    }
  }
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="225">Type </th><th>Description</th></tr></thead><tbody><tr><td><code>tier</code></td><td><a href="crowdfund.md#tier">Tier</a></td><td>The new tier configurations.</td></tr></tbody></table>

### RemoveTier

Removes the tier with the specified level.

{% hint style="warning" %}
Can onluy be used if the campaign has not started yet.

Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 RemoveTier { level: Uint64 },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_tier":{
    "level": "3"
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="266">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>level</code></td><td>Uint64</td><td>The tier level to remove from the campaign.</td></tr></tbody></table>

### StartCampaign

Starts the crowdfunding campaign so that users are able to start purchasing tiers when the `start_time`  is reached.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  StartCampaign {
        start_time: Option<MillisecondsExpiration>,
        end_time: MillisecondsExpiration,
        presale: Option<Vec<PresaleTierOrder>>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "start_campaign": {
        "start_time": "1672531199000", 
        "end_time": "1672534799000",    
        "presale": [
            {
                "level": "2",  
                "amount": "3", 
                "orderer": "andr1..." 
            },
            {
                "level": "1",  
                "amount": "1", 
                "orderer": "andr1..." 
            }
        ]
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="281">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>start_time</code></td><td>Optoion&#x3C;MillisecondsExpiration></td><td>The time that the campaign opens up for purchases.</td></tr><tr><td><code>end_time</code></td><td>MillisecondsExpiration</td><td>The time that the campaign ends.</td></tr><tr><td><code>presale</code></td><td>Option&#x3C;Vec&#x3C;<a href="crowdfund.md#presaletierorder">PresaleTierOrder</a>></td><td>A list of preorders for the campaign. The address that has preordered can directly claim their NFT.</td></tr></tbody></table>

#### PresaleTierOrder

```rust
pub struct PresaleTierOrder {
    pub level: Uint64,
    pub amount: Uint128,
    pub orderer: Addr,
}
```

|           |         |                                                        |
| --------- | ------- | ------------------------------------------------------ |
| `level`   | Uint64  | The tier level preordered.                             |
| `amount`  | Uint128 | The number of NFTs preordered for the specified level. |
| `orderer` | Addr    | The address of the user that preordered the tier NFT.  |

### PurchaseTiers (Native)

Purchase a tier in the campaign using native funds.

{% hint style="warning" %}
Make sure to attach the required funds along with the message.

Can only be called if the campaign is ongoing.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum ExecuteMsg {
<strong>PurchaseTiers {
</strong> orders: Vec&#x3C;SimpleTierOrder> 
   },
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
  "purchase_tiers": {
    "orders": [
      {
        "level": "1",
        "amount": "2"
      },
      {
        "level": "2",
        "amount": "1"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                                   | Description                              |
| -------- | ------------------------------------------------------ | ---------------------------------------- |
| `orders` | Vec<[SimpleTierOrders](crowdfund.md#simpletierorders)> | The tiers to purchase from the campaign. |

#### SimpleTierOrders

```rust
pub struct SimpleTierOrder {
    pub level: Uint64,
    pub amount: Uint128,
}
```

| Name     | Type    | Description                                    |
| -------- | ------- | ---------------------------------------------- |
| `level`  | Uint64  | The tier level to purchase from.               |
| `amount` | Uint128 | How many NFTs to but from the specified level. |

### Receive

Receives tokens from a CW20 [Send](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw20#send)  message to be used to purchase a tier in the campaign.

{% hint style="warning" %}
This message is not called by the user on this ADO, but is the case that handles receiving CW20 tokens from a CW20 ADO.

The JSON representation of the [PurchaseTiers](crowdfund.md#purchasetiers-cw20) message should be base64 encoded and specified as the msg field for the [Send](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw20#send) execute.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Receive(Cw20ReceiveMsg),
    }
    
pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}

pub enum Cw20HookMsg {
    PurchaseTiers { orders: Vec<SimpleTierOrder> },
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
In order to bid on an auction using a CW20, you need to define the message of the `Cw20ReceiveMsg` as a `Cw721HookMsg` as a base64 encoding of the JSON version.
{% endhint %}

### PurchaseTiers (CW20)

Purchase a tier in the campaign using the sent CW20 tokens.

{% hint style="warning" %}
Can only be called if the campaign is ongoing.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum ExecuteMsg {
<strong>PurchaseTiers {
</strong> orders: Vec&#x3C;SimpleTierOrder> 
   },
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
  "purchase_tiers": {
    "orders": [
      {
        "level": 1,
        "amount": "2"
      },
      {
        "level": 2,
        "amount": "1"
      }
    ]
  }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                                   | Description                              |
| -------- | ------------------------------------------------------ | ---------------------------------------- |
| `orders` | Vec<[SimpleTierOrders](crowdfund.md#simpletierorders)> | The tiers to purchase from the campaign. |

### Claim

Claim the NFTs purchased in the campaign. If the campaign was not successfullm the message will issue a refund for the user.

{% hint style="warning" %}
Users can only claim when the campaign is over or if it was unsuccessfull.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Claim{},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim":{}
}
```
{% endtab %}
{% endtabs %}

### EndCampaign

Ends the campaign.

{% hint style="warning" %}
Only available to the contract owner.

A campaign can be ended if it is ongoing and has hit the `soft_cap` or before it starts.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
      EndCampaign {},
      }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"end_campaign":{}
}
```
{% endtab %}
{% endtabs %}

### DiscardCampaign

Discards a campaign. Used in case of emergencies where the owner discovers a mistake in the configurations.

{% hint style="warning" %}
Only available to the contract owner.

A campaign can be discarded if it is ongoing or before it starts.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     DiscardCampaign {},
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"discard_campaign":{}
}
```
{% endtab %}
{% endtabs %}

### Base Executes

The rest of the execute messages can be found in the [ADO Base](../platform-and-framework/ado-base/andromedamsg.md) section.

## QueryMsg

### CampaignSummary

Gets a summary for all the campaign information.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(CampaignSummaryResponse)]
    CampaignSummary {},
    }
```
{% endtab %}

{% tab title="JSON" %}

{% endtab %}
{% endtabs %}

#### CampaignSummaryResponse

```rust
pub struct CampaignSummaryResponse {
    pub title: String,
    pub description: String,
    pub banner: String,
    pub url: String,
    pub token_address: AndrAddr,
    pub denom: Asset,
    pub withdrawal_recipient: Recipient,
    pub soft_cap: Option<Uint128>,
    pub hard_cap: Option<Uint128>,
    pub start_time: Option<MillisecondsExpiration>,
    pub end_time: MillisecondsExpiration,
    // Current Status
    pub current_stage: String,
    pub current_cap: u128,
}
```

**`current_stage`:** A campaign can be one of four stages.

```rust
#[cw_serde]
pub enum CampaignStage {
    /// Stage when all necessary environment is set to start campaign
    READY,
    /// Stage when campaign is being carried out
    ONGOING,
    /// Stage when campaign is finished successfully
    SUCCESS,
    /// Stage when campaign failed to meet the target cap before expiration
    FAILED,
}
```

**`current_cap`:** The current amount of capital made by the campaign.

{% hint style="info" %}
The rest of the fields are the same sa the ones set at instantiation.
{% endhint %}

### TierOrders

Gets a summary for all the campaign information.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(TierOrdersResponse)]
    TierOrders {
        orderer: String,
        start_after: Option<u64>,
        limit: Option<u32>,
        order_by: Option<OrderBy>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "tier_orders": {
    "orderer": "andr1...",
    "start_after": 3,
    "limit": 20,
    "order_by": "asc"
  }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="184">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>orderer</code></td><td>String</td><td>The address of the user we are getting the orders for.</td></tr><tr><td><code>start_after</code></td><td>Option&#x3C;u64></td><td>Optional parameter to indicate the starting point for pagination, based on the level of the TierOrder.</td></tr><tr><td><code>limit</code></td><td>Option&#x3C;u32></td><td>Optional parameter to limit the number of results.</td></tr><tr><td><code>order_by</code></td><td>Option&#x3C;OrderBy></td><td>Optional parameter to specify the ordering of the results.</td></tr></tbody></table>

#### OrderBy

```rust
pub enum OrderBy {
    Asc,
    Desc,
}
```

#### TierOrdersResponse

```rust
pub struct TierOrdersResponse {
    pub orders: Vec<SimpleTierOrder>,
}

pub struct SimpleTierOrder {
    // The tier level
    pub level: Uint64,
    // The number of NFTs sold for this tier level
    pub amount: Uint128,
}
```

### Tiers

Gets a summary for all the campaign tiers.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(TiersResponse)]
    Tiers {
        start_after: Option<u64>,
        limit: Option<u32>,
        order_by: Option<OrderBy>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "tiers": {
    "start_after": 1,
    "limit": 50,
    "order_by": "asc"
  }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="184">Name</th><th>Type</th><th></th></tr></thead><tbody><tr><td><code>start_after</code></td><td>Option&#x3C;u64></td><td>Optional parameter to indicate the starting point for pagination, based on the level of the TierOrder.</td></tr><tr><td><code>limit</code></td><td>Option&#x3C;u32></td><td>Optional parameter to limit the number of results.</td></tr><tr><td><code>order_by</code></td><td>Option&#x3C;OrderBy></td><td>Optional parameter to specify the ordering of the results.</td></tr></tbody></table>

#### TierResponse

```rust
pub struct TiersResponse {
    pub tiers: Vec<Tier>,
}
```

Returns a [Tier](crowdfund.md#tier) struct for every available tier.

### Base Queries

The rest of the query messages can be found in the [ADO Base](../platform-and-framework/ado-base/andromedaquery.md) section.
