---
description: >-
  An ADO contract to facilitate auctioning an Andromeda token. This can be added
  as a module to it.
---

# Auction

## Introduction

The **Auction** ADO is a smart contract that allows performing auctions on NFT tokens. The owner can send an NFT to this contract with the required messages to start an auction on it. Once the auction has started users can place bids on the token until the auction expires. The highest bid will win the auction sending the funds to the seller and receiving the token in return.

**Ado\_type**: auction

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
    pub whitelist: Option<Vec<Addr>>
    pub owner: String,
    pub token_id:String,
    pub token_address:String,
    pub is_cancelled:bool,
}
```

## InstantiateMsg <a href="#instantiatemsg" id="instantiatemsg"></a>

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
<strong>    pub modules: Option&#x3C;Vec&#x3C;Module>>,
</strong> }</code></pre>
{% endtab %}
{% endtabs %}

| Name      | Type                  | Description                                                                                                                                                     |
| --------- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `modules` | Option\<Vec\<Module>> | An optional vector of Andromeda[ Modules](broken-reference) that can be attached to the contract. "rates", "address-list", and  "receipt" modules can be added. |

## ExecuteMsg

### ReceiveNft

Receives a token from a [`SendNft`](../../andromeda-digital-objects/andromeda-digital-object.md#sendnft) and starts an auction based on the given parameters in the [`StartAuction` ](auction.md#startauction)struct. &#x20;

{% hint style="warning" %}
The auction information can be modified before it has started but is immutable after that.

Only the NFT owner can send the NFT and start the auction.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub struct Cw721ReceiveMsg {
        pub sender: String,
        pub token_id: String,
        pub msg: Binary,
    }

pub enum ExecuteMsg {
    ReceiveNft(Cw721ReceiveMsg)
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
In order to start an auction you need to define the message of the `Cw721ReceiveMsg` as a `Cw721HookMsg`.
{% endhint %}

#### StartAuction

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw721HookMsg {
    StartAuction {
        start_time: Expiration,
        end_time: Expiration,
        coin_denom: String,
        whitelist: Option<Vec<Addr>>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "start_auction": {
          "start_time": { "at_height": 500 },
          "end_time": { "at_height": 600 },
          "coin_denom": "uusd",
          "whitelist": ["juno1...", "juno1...", ...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                  | Description                                                                          |
| ------------ | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `start_time` | [Expiration](../../platform-and-framework/common-types/expiration.md) | The start of the auction.                                                            |
| `end_time`   | [Expiration](../../platform-and-framework/common-types/expiration.md) | The end of the auction.                                                              |
| `coin_denom` | String                                                                | The native coin denomination to do the auction in.                                   |
| `whitelist`  | Option\<Vec\<Addr>>                                                   | Optional list of addresses to whitelist for the auction. If None, auction is public. |

{% hint style="warning" %}
To be a valid auction the following requirements must be met:

* `start_time` occurs not in the past
* `start_time` and `end_time` use the same variant of `Expiration`, (but not `Expiration::Never {})`
* `start_time` < `end_time`
{% endhint %}

### UpdateAuction

Updates the information of an auction.

{% hint style="info" %}
Only the owner of the auction can execute `UpdateAuction`.

An auction can be updated only if it has not started yet.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
 UpdateAuction {
        token_id: String,
        token_address: String,
        start_time: Expiration,
        end_time: Expiration,
        coin_denom: String,
        whitelist: Option<Vec<Addr>>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "update_auction": {
  "token_id":"token_001",
   "token_address":"juno1...",
   "start_time": { "at_height": 500 },
   "end_time": { "at_height": 600 },
   "coin_denom": "uusd",
   "whitelist": ["juno1...", "juno1...", ...]
    }
 }
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
* `start_time` occurs not in the past
* `start_time` and `end_time` use the same variant of `Expiration`, (but not `Expiration::Never {})`
* `start_time` < `end_time`
{% endhint %}

| Name            | Type                                                                  | Description                                                                          |
| --------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| `token_id`      | String                                                                | The id of the NFT that is being auctioned.                                           |
| `token_address` | String                                                                | The address of the token contract.                                                   |
| `start_time`    | [Expiration](../../platform-and-framework/common-types/expiration.md) | The start of the auction.                                                            |
| `end_time`      | [Expiration](../../platform-and-framework/common-types/expiration.md) | The end of the auction.                                                              |
| `coin_denom`    | String                                                                | The native coin denomination to do the auction in.                                   |
| `whitelist`     | Option\<Vec\<Addr>>                                                   | Optional list of addresses to whitelist for the auction. If None, auction is public. |

### CancelAuction

{% hint style="info" %}
Only the owner of the auction can execute `CancelAuction`.
{% endhint %}

Cancels the auction of a token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    CancelAuction {
        token_id: String,
        token_address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "cancel_auction": {
    "token_id":"token_001",
    "token_address":"juno1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                       |
| --------------- | ------ | ------------------------------------------------- |
| `token_id`      | String | The id of the NFT in the auction to be cancelled. |
| `token_address` | String | The address of the token contract.                |

### PlaceBid

Places a bid for the auction for the given NFT id. The bid must be sent as native funds along with this message. The previous largest bid gets automatically sent back to the bidder when they are outbid.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    PlaceBid {
        token_id: String,
        token_address:String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "place_bid": {
        "token_id": "token_001",
        "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                          |
| --------------- | ------ | ------------------------------------ |
| `token_id`      | String | The id of the NFT to place a bid on. |
| `token_address` | String | The address of the token contract.   |

{% hint style="warning" %}
The following criteria must be met for the bid to be placed:

* The ADO is currently under auction
* The sender's bid is higher than the highest bid
* The sender does not currently hold the highest bid
* The sender is not the token owner
{% endhint %}

### Claim

Sends the winner of the auction the NFT and the funds to the NFT owner  when the auction has finished. Anyone is allowed to execute this message.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Claim {
        token_id: String,
        token_address:String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "claim": {
        "token_id": "token_001",
        "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `token_id`      | String | The id of the token that was auctioned. |
| `token_address` | String | The address of the token contract.      |

{% hint style="warning" %}
Can only be done when the `end_time` has been passed. If no bids has been placed the original owner retains the token.
{% endhint %}

### AndrRecieve

Check [AndrReceive](../../platform-and-framework/ado\_base.md#andrrecieve).

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
        token_address:String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "latest_auction_state": {
        "token_id": "token_001",
        "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                             |
| --------------- | ------ | ------------------------------------------------------- |
| `token_id`      | String | The id of the NFT that we want to query the auction of. |
| `token_address` | String | The address of the token contract.                      |

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
    pub is_cancelled:bool, 
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
          "high_bidder_addr": "juno1...",
          "high_bidder_amount": "50",
          "auction_id": "0",
          "coin_denom": "uusd",
          "claimed": false,
          "whitelist": ["juno1...", "juno1...", ...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                                  | Description                                                           |
| -------------------- | --------------------------------------------------------------------- | --------------------------------------------------------------------- |
| `start_time`         | [Expiration](../../platform-and-framework/common-types/expiration.md) | The start of the auction.                                             |
| `end_time`           | [Expiration](../../platform-and-framework/common-types/expiration.md) | The end of the auction.                                               |
| `high_bidder_addr`   | String                                                                | The terra address of the highest bidder.                              |
| `high_bidder_amount` | Uint128                                                               | The amount of the highest bid.                                        |
| `auction_id`         | Uint128                                                               | The id of the auction.                                                |
| `coin_denom`         | String                                                                | The denom the auction is in.                                          |
| `is_cancelled`       | bool                                                                  | Whether or not the auction has been cancelled.                        |
| `whitelist`          | Option\<Vec\<Addr>>                                                   | The whitelisted addresses if they were specified at time of creation. |

### AuctionState

Gets the auction state for a particular `auction_id`.

{% hint style="warning" %}
Each Auction has an auction\_id which starts at 1 and increments every new auction.

To get the auction\_id of a particular token, use LatestAuctionState
{% endhint %}

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

#### Response

See [AuctionStateResponse](auction.md#auctionstateresponse).

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
```json
{
 "bids":{
    "auction_id": "4",
    "start_after": 3,
    "limit": 25
    }
 }
```
{% endtab %}
{% endtabs %}

| Name          | Type             | Description                                                                                            |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------ |
| `auction_id`  | Uint128          | The auction id.                                                                                        |
| `start_after` | Option\<u64>     | Optional parameter to specify which bid to start after. If none specified index `0` will be used.      |
| `limit`       | Option\<u64>     | Optional parameter to specify how many bids to query. If none specified a default limit of 10 is used. |
| `order_by`    | Option\<OrderBy> | Optional parameter to specify the order of the bids being queries. Default is Ascending.               |

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum OrderBy {
    Asc,
    Desc,
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
                "bidder": "juno1...",
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

#### Bid

The state for a particular bid is stored in a basic struct.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Bid {
    pub bidder: String,
    pub amount: Uint128,
    pub timestamp: Timestamp,
}
```

| Name        | Type                                                                           |                            |
| ----------- | ------------------------------------------------------------------------------ | -------------------------- |
| `bidder`    | String                                                                         | The address of the bidder. |
| `amount`    | Uint128                                                                        | The amount of funds bid.   |
| `timestamp` | [TimeStamp](../../platform-and-framework/common-types/expiration.md#timestamp) | The time of the bid.       |

### AuctionIds

Queries the auction ids for a given token.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    AuctionIds {
        token_id: String,
        token_address:String
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_ids": {
        "token_id": "token_001",
        "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                            |
| --------------- | ------ | -------------------------------------- |
| `token_id`      | String | The id of the token/NFT.               |
| `token_address` | String | The address of the token/NFT contract. |

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

### AuctionInfosForAddress

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg {
    AuctionInfosForAddress {
        token_address: String,
        start_after: Option<String>,
        limit: Option<u64>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"auction_infos_for_address":{
       "token_address":"juno1...",
       "start_after":"3",
       "limit": 15
       }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type            | Description                                                                                                                              |
| --------------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `token_address` | String          | The address of the token contract                                                                                                        |
| `start_after`   | Option\<String> | Optional parameter to specify which `AuctionInfo` to start from. If none specified index `0` will be used.                               |
| `limit`         | Option\<u64>    | Optional parameter to specify how many `AuctionInfo` to query. If none specified a default limit of 10 is used. The maximum limit is 30. |

#### AuctionInfosForAddressResponse

Returns a vector of AuctionInfo defined below.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AuctionInfo {
    pub auction_ids: Vec<Uint128>,
    pub token_address: String,
    pub token_id: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{

"auction_info" {
   "auction_ids": ["0","1",...],
   "token_address":"juno1...",
   "token_id":"token_001"
   }
}
   
```
{% endtab %}
{% endtabs %}

| Name            | Type          | Description                                                     |
| --------------- | ------------- | --------------------------------------------------------------- |
| `auction_ids`   | Vec\<Uint128> | The ids of the auctions that use  the specified `token_address` |
| `token_address` | String        | The address of the token contract.                              |
| `token_id`      | String        | The id of the token that was auctioned.                         |

### AndrQuery

Check [AndrQuery](../../platform-and-framework/ado\_base.md#andrquery).
