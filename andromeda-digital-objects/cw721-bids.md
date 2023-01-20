# CW721 Bids

## Introduction

The **CW721 Bids ADO** is a smart contract used to buy/sell NFT tokens. It allows users to place a bid on a certain token which can then be accepted by the seller if satisfied. Once a bid is placed, funds are allocated for the purchase until the bid is expired, accepted, canceled, or a higher bid has been made.

The Bids ADO  is not built to work as a standalone contract, instead it is implemented as a [module](../modules/cw721-bids-module.md) for the NFT Collectible ADO facilitating the process of trading NFTs.&#x20;

**Ado\_type**: cw721-bids

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub andromeda_cw721_contract: String,
    pub valid_demom: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andromeda_cw721_contract":"andr1...",
"valid_denom":"uandr"
}
```
{% endtab %}
{% endtabs %}

| Name                       | Type   | Description                                                                |
| -------------------------- | ------ | -------------------------------------------------------------------------- |
| `andromeda_cw721_contract` | String | The address of the contract allowed to sell tokens using the bid contract. |
| `valid_denom`              | String | The denom allowed in bids.                                                 |

## ExecuteMsg

### PlaceBid

Places a bid on the token with the specified `token_id`. When a bid is placed, the funds are taken and returned in the case of a higher bid or cancelation.

{% hint style="warning" %}
The ADO owner is not allowed to place a bid on their ADO.

`bid_amount` needs to exceed the previous highest bid.

The funds must match the specified `valid_denom` in instantiation.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
 PlaceBid {
        token_id: String,
        expiration: Expiration,
        bid_amount: Uint128,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"place_bid":{
  "token_id":"1",
  "expiration":{
     "at_height": 300
     }
  "bid_amount": "200000"
  }
}
  
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                               | Description                                |
| ------------ | ------------------------------------------------------------------ | ------------------------------------------ |
| `token_id`   | String                                                             | The token id of the token/NFT to buy.      |
| `expiration` | [Expiration](../platform-and-framework/common-types.md#expiration) | An expiration for the bid.                 |
| `bid_amount` | Uint128                                                            | The amount of funds to bid for the token.  |

### CancelBid

Cancels a bid previously placed by a purchaser.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  CancelBid {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"cancel_bid":{
  "token_id": "1"
  }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                |
| ---------- | ------ | ------------------------------------------ |
| `token_id` | String | The id of the token to remove the bid on.  |

### AcceptBid

Accepts a bid that is placed on your NFT.&#x20;

{% hint style="warning" %}
Only the `andromeda_cw721_contract` can accept bids.

Cannot accept a bid on a token that has a current [TransferAgreement](cw721.md#transferagreement).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  AcceptBid {
        token_id: String,
        recipient: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"accept_bid":{
  "token_id":"1",
   "recipient":"andr1..."
   }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                  |
| ----------- | ------ | -------------------------------------------- |
| `token_id`  | String | The Id of the token to accept the bid on.    |
| `recipient` | String | The address to receive the funds of the bid. |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### Bid

Returns the latest bid of the token with the given `token_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
  #[returns(BidResponse)]
  Bid {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"bid":{
  "token_id":"1"
   }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                         |
| ---------- | ------ | --------------------------------------------------- |
| `token_id` | String | The id of the token/NFT to get the latest  bid on.  |

#### BidResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct BidResponse {
    pub denom: String,
    pub bid_amount: Uint128,
    pub remaining_amount: Uint128,
    pub tax_amount: Uint128,
    pub expiration: Expiration,
    pub purchaser: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"denom":"uandr",
"bid_amount":"1000",
"remaining_amount":"997",
"tax_amount":"3",
"expiration":{
    "at_height": 500
    },
"purchaser":"andr1..."
}



```
{% endtab %}
{% endtabs %}

| Name               | Type                                                               | Description                                                                      |
| ------------------ | ------------------------------------------------------------------ | -------------------------------------------------------------------------------- |
| `denom`            | String                                                             | The denomination of the coin used to place bids.                                 |
| `bid_amount`       | Uint128                                                            | The amount of coins bid on the token.                                            |
| `remaining_amount` | Uint128                                                            | The amount left after any royalties/taxes have been applied to the `bid_amount.` |
| `tax_amount`       | Uint128                                                            | The amount of coins taken as tax.                                                |
| `expiration`       | [Expiration](../platform-and-framework/common-types.md#expiration) | Expiration for the bid.                                                          |
| `purchaser`        | String                                                             | The address that has placed the bid.                                             |

### AllBids

Returns all the bids of a certain purchaser.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
   #[returns(AllBidsResponse)]
   AllBids {
        purchaser: String,
        limit: Option<u32>,
        start_after: Option<String>,
    }
 } 
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_bids":{
  "purchaser":"andr1...",
  "limit": 15
    }
 }
  
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                    |
| ------------- | --------------- | ---------------------------------------------------------------------------------------------- |
| `purchaser`   | String          | The address of the purchaser we want to get the bids of.                                       |
| `limit`       | Option\<u32>    | An optional limit to the number of bids queried. The default limit is 10. The max limit is 30. |
| `start_after` | Option\<String> | An optional ID for which to start after, used for pagination.                                  |

#### AllBidsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllBidsResponse {
    pub bids: Vec<BidResponse>,
}
```
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
"bids":[ 
 {
  "denom":"uandr",
  "bid_amount":"1000",
  "remaining_amount":"997",
  "tax_amount":"3",
<strong>  "expiration":{
</strong>    "at_height": 500
    },
  "purchaser":"andr1..."
},
...
<strong>  ]
</strong><strong>}
</strong></code></pre>
{% endtab %}
{% endtabs %}

| Name   | Type                                            | Description                                                               |
| ------ | ----------------------------------------------- | ------------------------------------------------------------------------- |
| `bids` | Vec<[BidResponse](cw721-bids.md#offerresponse)> | A vector of BidResponse containing each bid with the related information. |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
