---
description: >-
  An ADO contract to facilitate auctioning an Andromeda token. This can be added
  as a module to it.
---

# Auction

## TokenAuctionState

The state for an auction is stored in a basic struct

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct TokenAuctionState {
    pub start_time: Expiration,
    pub end_time: Expiration,
    pub high_bidder_addr: Addr,
    pub high_bidder_amount: Uint128,
    pub coin_denom: String,
    pub auction_id: Uint128,
    pub claimed: bool,
    pub whitelist: Option<Vec<Addr>>,
}
```

## Bid

The state for a particular bid is stored in a basic struct.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Bid {
    pub bidder: String,
    pub amount: Uint128,
    pub timestamp: Timestamp,
}
```

## InstantiateMsg <a href="#instantiatemsg" id="instantiatemsg"></a>

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub token_addr: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "token_addr": "terra1...",
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description                                    |
| ------------ | ------ | ---------------------------------------------- |
| `token_addr` | String | The address of the corresponding ADO address.  |

## ExecuteMsg

### StartAuction

Starts an auction for the given token or updates the auction's details if it hasn't started yet.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(renamste_all = "snake_case")]
pub enum ExecuteMsg {
     StartAuction {
          token_id: String,
          start_time: Expiration,
          end_time: Expiration,
          coin_denom: String,
          whitelist: Option<Vec<Addr>>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "start_auction": {
          "token_id": "token_001",
          "start_time": { "at_height": 500 },
          "end_time": { "at_height": 600 },
          "coin_denom": "uusd",
          "whitelist": ["terra1...", "terra1...", ...],
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                | Description                                                                          |
| ------------ | ------------------- | ------------------------------------------------------------------------------------ |
| `token_id`   | String              | The id of the ADO to auction.                                                        |
| `start_time` | Expiration          | The start of the auction.                                                            |
| `end_time`   | Expiration          | The end of the auction.                                                              |
| `coin_denom` | String              | The native coin denomination to do the auction in                                    |
| `whitelist`  | Option\<Vec\<Addr>> | Optional list of addresses to whitelist for the auction. If None, auction is public. |

{% hint style="warning" %}
To be a valid auction the following requirements must be met:

* `start_time` occurs not in the past
* `start_time` and `end_time` use the same variant of `Expiration`, (but not `Expiration::Never {})`
* `start_time` < `end_time`
* The sender of the transaction owns the ADO with`token_id`
* No auction is currently ongoing for ADO with`token_id`
{% endhint %}

### PlaceBid

Places a bid for the auction for the given ADO id. The bid must be sent as native funds along with this message. If this is not the first bid, the previous bid is sent back to the user who placed it.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(renamste_all = "snake_case")]
pub enum ExecuteMsg {
    PlaceBid {
        token_id: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "place_bid": {
        "token_id": "token_001"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description        |
| ---------- | ------ | ------------------ |
| `token_id` | String | The id of the ADO. |

{% hint style="warning" %}
The following criteria must be met for the bid to be placed:

* The ADO is currently under auction
* The sender's bid is higher than the highest bid
* The sender does not currently hold the highest bid
{% endhint %}

### Claim

Sends the winner of the auction the ADO and the funds to the ADO owner via a `transfer_agreement` when the auction has finished. Anyone is allowed to execute this message.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(renamste_all = "snake_case")]
pub enum ExecuteMsg {
    Claim {
        token_id: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "claim": {
        "token_id": "token_001"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                             |
| ---------- | ------ | --------------------------------------- |
| `token_id` | String | The id of the token that was auctioned. |

{% hint style="warning" %}
Can only be done when the `end_time` has been passed. If no bids has been placed the original owner retains the token.
{% endhint %}

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### LatestAuctionState

Queries the most recent auction for the given token (either ongoing, complete, or not started yet).

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    LatestAuctionState {
        token_id: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "latest_auction_state": {
        "token_id": "token_001"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description   |
| ---------- | ------ | ------------- |
| `token_id` | String | The token id. |

#### AuctionStateResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct AuctionStateResponse {
    pub start_time: Expiration,
    pub end_time: Expiration,
    pub high_bidder_addr: String,
    pub high_bidder_amount: Uint128,
    pub auction_id: Uint128,
    pub coin_denom: String,
    pub claimed: bool,
    pub whitelist: Option<Vec<Addr>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_state_response": {
          "start_time": { "at_height": 500 },
          "end_time": { "at_height": 600 },
          "high_bidder_addr": "terra1...",
          "high_bidder_amount": "50",
          "auction_id": "0",
          "coin_denom": "uusd",
          "claimed": false,
          "whitelist": ["terra1...", "terra1...", ...],
    }
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                | Description                                                                                         |
| -------------------- | ------------------- | --------------------------------------------------------------------------------------------------- |
| `start_time`         | Expiration          | The start of the auction.                                                                           |
| `end_time`           | Expiration          | The end of the auction.                                                                             |
| `high_bidder_addr`   | String              | The terra address of the highest bidder.                                                            |
| `high_bidder_amount` | Uint128             | The amount of the highest bid.                                                                      |
| `auction_id`         | Uint128             | The id of the auction.                                                                              |
| `coin_denom`         | String              | The denom the auction is in.                                                                        |
| `claimed`            | bool                | Whether or not the auction has been claimed. If `true` a new auction for this token can be created. |
| `whitelist`          | Option\<Vec\<Addr>> | The whitelisted addresses if they were specified at time of creation.                               |

### AuctionState

Gets the auction state for a particular `auction_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    AuctionState {
        auction_id: Uint128,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_state": {
        "auction_id": "0"
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type    | Description     |
| ------------ | ------- | --------------- |
| `auction_id` | Uint128 | The auction id. |

See [TokenAuctionStateResponse](auction.md#auctionstateresponse).



### Bids

Gets the bids for a given auction.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Bids {
        auction_id: Uint128,
        start_after: Option<u64>,
        limit: Option<u64>,
        order_by: Option<OrderBy>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}

{% endtab %}
{% endtabs %}

| Name          | Type             | Description                                                                                          |
| ------------- | ---------------- | ---------------------------------------------------------------------------------------------------- |
| `auction_id`  | Uint128          | The auction id.                                                                                      |
| `start_after` | Option\<u64>     | Optional parameter to specify which bid to start after. If none specified index `0` will be used.    |
| `limit`       | Option\<u64>     | Optional parameter to specify how many bids to query. If none specified a max limit of `30` is used. |
| `order_by`    | Option\<OrderBy> | Optional parameter to specify the order of the bids being queries. Default is Ascending.             |

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct OrderBy {
    Asc,
    Desc
}
```

#### BidsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct BidsResponse {
    pub bids: Vec<Bid>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "bids_response": {
        "bids": [ 
            { 
                "bidder": "terra1...",
                "amount": "500",
                "timestamp": "60"
            }
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type                       | Description         |
| ------ | -------------------------- | ------------------- |
| `bids` | Vec<[Bid](auction.md#bid)> | The retrieved bids. |

### AuctionIds

Queries the auction ids for a given token.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    AuctionIds {
        token_id: String
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_ids": {
        "token_id": "token_001"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description |
| ---------- | ------ | ----------- |
| `token_id` | String | The ADO id. |

#### AuctionIdsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct AuctionIdsResponse {
    pub auction_ids: Vec<Uint128>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_ids_response": {
        "auction_ids": ["0", "3", ... ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type          | Description      |
| ------------- | ------------- | ---------------- |
| `auction_ids` | Vec\<Uint128> | The auction ids. |

### Config

Queries the config for the ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Config {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "config": {}
}
```
{% endtab %}
{% endtabs %}

#### ConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct ConfigResponse {
    pub token_addr: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "config_response": {
        "token_addr": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description                      |
| ------------ | ------ | -------------------------------- |
| `token_addr` | String | The address of the ADO contract. |

### Owner

See [Ownership](ownership.md#executemsg).
