# Auction V1.0.0

## Introduction

The **Auction** ADO is a smart contract that allows performing custom auctions on NFTs. NFTs can be sent to this contract with the required messages to start an auction on it. Once the auction has started, users can place bids on the token until the auction expires. The highest bid will win the auction, sending the funds to the seller and receiving the token in return.&#x20;

There are multiple ways to customize this ADO:

* **Authorized:** Only the NFT contracts sepcified at instantiation are allowed to send NFTs to this Auction ADO.&#x20;
* **Open:** This means that any NFT contract is allowed to send an NFT to this ADO to be auctioned. To have the Auction ADO be open, do not specify  `authorized_token_addresses` in instantiation.

Bidding on the NFT can also be customized to work with one of the following options:

* **Native:** By specifying the denom of the chain in the [StartAuction](auction-v1.0.0.md#startauction) message.&#x20;
* **CW20:** By specifying the contract address of the CW20 token to be used in the StartAuction. The CW20 tokens allowed to be set as the bidding token can be restricted by specifying `authorized_cw20_address` at instantiation. If this is not specified, then any CW20 token can be set.

{% hint style="info" %}
This ADO allows creating [English Auctions](https://en.wikipedia.org/wiki/English\_auction).&#x20;
{% endhint %}

**Ado\_type**: auction

## InstantiateMsg <a href="#instantiatemsg" id="instantiatemsg"></a>

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub struct InstantiateMsg {
    pub authorized_token_addresses: Option&#x3C;Vec&#x3C;AndrAddr>>,
    pub authorized_cw20_address: Option&#x3C;AndrAddr>,
<strong>    pub kernel_address: String,
</strong><strong>    pub owner: Option&#x3C;String>,
</strong> }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorized_token_addresses":["andr1...","andr2..."],
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name                         | Type                                                                         | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `authorized_token_addresses` | Option\<Vec<[AndrAddr](../platform-and-framework/common-types.md#andraddr)>> | Optional set of CW721 contract addresses to be allowed to send NFTs to the Auction ADO. If not specified, then any CW721 can send NFTs to the auction.                                                                                                                                                                        |
| `authorized_cw20_address`    | Optoin<[AndrAddr](../platform-and-framework/common-types.md#andraddr)>       | Optional CW20 address to authorize to be used as the purchasing coin for the NFTs in auction. If not specified, then any CW20 can be set as the purchasing coin.                                                                                                                                                              |
| `kernel_address`             | String                                                                       | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`                      | Option\<String>                                                              | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

## ExecuteMsg

### ReceiveNft

Receives a token from a [`SendNft`](cw721-v1.0.0.md#sendnft) and starts an auction based on the given parameters in the [`StartAuction` ](auction-v1.0.0.md#startauction)struct. &#x20;

{% hint style="warning" %}
The auction information can be modified before it has started but is immutable after that.

Only the NFT owner can send the NFT and start the auction.

This message is not called by the user on this ADO, but is the case that handles receiving NFTs from a CW721 ADO.
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
    }
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
In order to start an auction you need to define the message of the `Cw721ReceiveMsg` as a `Cw721HookMsg`.
{% endhint %}

### StartAuction

Starts an auction sale on the sent NFT.

{% hint style="warning" %}
You need to get the base64 encoded representation of the JSON message and attach it as the `msg`when sending. &#x20;
{% endhint %}

{% hint style="warning" %}
`start_time` should not be a time in the past.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw721HookMsg {
    StartAuction {
        start_time: Option<MillisecondsExpiration>,
        end_time: MillisecondsExpiration,
        coin_denom: String,
        uses_cw20: bool,
        min_bid: Option<Uint128>
        whitelist: Option<Vec<Addr>>,
        recipient: Option<Recipient>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "start_auction": {
          "start_time": 1663334970211,
          "end_time": 1763334970211,
          "coin_denom": "uandr",
          "uses_cw20": false,
          "min_bid":"300",
          "whitelist": ["andr1...", "andr1...", ...],
          "recipient":{
                "address":"andr1..."
                }
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                                     | Description                                                                                                                                            |
| ------------ | ---------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `start_time` | Option<[MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds)> | Optional Start time for the sale specified as a [timestamp](https://www.epochconverter.com) in milliseconds. Defaults to immediately if not specified. |
| `end_time`   | [MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds)         | [Timestamp](https://www.epochconverter.com) in milliseconds for when the sale ends.                                                                    |
| `coin_denom` | String                                                                                   | The coin denomination to be used to bid on the NFT. Can be either a native coin ie."**uandr**" or a CW20 token address ie. "**andr1...**"              |
| `uses_cw20`  | bool                                                                                     | Whether a CW20 is used to bid on the NFT. If set to false, then a native token is used.                                                                |
| `min_bid`    | Option\<Uint128>                                                                         | The minimum bid that can be placed on the auctioned token.                                                                                             |
| `whitelist`  | Option\<Vec\<Addr>>                                                                      | Optional list of addresses to whitelist for the auction. If None, auction is public.                                                                   |
| `recipient`  | Option<[Recipient](../platform-and-framework/common-types.md#recipient)>                 | An optional recipient to receive the sale funds for the sold NFT.                                                                                      |

***

### Receive

Receives tokens from a CW20 [Send](cw20-v1.0.0.md#send)  message to be used as a bid on the NFT auction.

{% hint style="warning" %}
This message is not called by the user on this ADO, but is the case that handles receiving CW20 tokens from a CW20 ADO.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}

pub enum ExecuteMsg {
     Receive(Cw20ReceiveMsg),
    }
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
In order to bid on an auction using a CW20, you need to define the message of the `Cw20ReceiveMsg` as a `Cw721HookMsg`.
{% endhint %}

### PlaceBid (CW20)

[Places a bid on the NFT auction using  CW20 tokens.](#user-content-fn-1)[^1]

{% hint style="warning" %}
You need to get the base64 encoded representation of the JSON message and attach it as the `msg` field for the CW20 **Send** message.

The following criteria must be met for the bid to be placed:

* The NFT is currently under auction
* The sender's bid is higher than the highest bid
* The sender does not currently hold the highest bid
* The sender is not the token owner
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    PlaceBid {
        token_id: String,
        token_address: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"place_bid":{
    "token_id":"2",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                         |
| --------------- | ------ | --------------------------------------------------- |
| `token_id`      | String | The token id of the NFT you want to place a bid on. |
| `token_address` | String | The address of the NFT contract the NFT belongs to. |

***

### AuthorizeTokenContract

Authorize a CW721 contract to send NFTs to this ADO.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     AuthorizeTokenContract {
        addr: AndrAddr,
        expiration: Option<MillisecondsExpiration>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorize_token_contract":{
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                                     | Description                                                    |
| ------------ | ---------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `addr`       | [AndrAddr](../platform-and-framework/common-types.md#andraddr)                           | The contract address of the CW721 (NFT) contract to authorize. |
| `expiration` | Option<[MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds)> | An optional expiration for the permission.                     |

### DeauthorizeTokenContract

Removes authorization from a CW721 contract to send NFTs to the auction.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    DeauthorizeTokenContract {
        addr: AndrAddr,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deauthorize_auction_contract":{
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type                                                           | Description                                                                   |
| ------ | -------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `addr` | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The contract address of the CW721 (NFT) contract to remove authorization for. |

### UpdateAuction

Updates the information of an auction.

{% hint style="warning" %}
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
        start_time: Option<MillisecondsExpiration>,
        end_time: MillisecondsExpiration,
        coin_denom: String,
        uses_cw20: bool,
        min_bid: Option<Uint128>
        whitelist: Option<Vec<Addr>>,
        recipient: Option<Recipient>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "update_auction": {
  "token_id":"1",
   "token_address":"andr1...",
   "start_time": 1663334970211,
   "duration": 1763334970211,
   "coin_denom": "uandr",
   "uses_cw20": false,
   "min_bid":"400",
   "whitelist": ["andr1...", "andr1...", ...]
    }
 }
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
`start_time` should not be a time in the past.
{% endhint %}

<table><thead><tr><th width="196.33333333333331">Name </th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>token_id</code></td><td>String</td><td>The Id of the NFT that is being auctioned.</td></tr><tr><td><code>token_address</code></td><td>String</td><td>The address of the  NFT contract.</td></tr><tr><td><code>start_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a>></td><td>Start time in milliseconds since <a href="https://www.epochconverter.com/clock">epoch</a>.</td></tr><tr><td><code>end_time</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a></td><td>Duration in milliseconds from the <code>start_time</code>.</td></tr><tr><td><code>coin_denom</code></td><td>String</td><td>The coin denomination to be used to bid on the NFT. Can be either a native coin ie."<strong>uandr</strong>" or a CW20 token address ie. "<strong>andr1...</strong>"</td></tr><tr><td><code>uses_cw20</code></td><td>bool</td><td>Whether a CW20 is used to bid on the NFT. If set to false, then a native token is used.</td></tr><tr><td><code>min_bid</code></td><td>Option&#x3C;Uint128></td><td>The minimum bid that can be placed on the auctioned token.</td></tr><tr><td><code>whitelist</code></td><td>Option&#x3C;Vec&#x3C;Addr>></td><td>Optional list of addresses to whitelist for the auction. If None, auction is public.</td></tr><tr><td><code>recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#recipient">Recipient</a>></td><td>An optional recipient to receive the sale funds for the sold NFT.</td></tr></tbody></table>

### CancelAuction

{% hint style="warning" %}
Only the owner of the auction can execute `CancelAuction`.

An auction cannot be canceled after it has started.&#x20;
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
    "token_address":"andr1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                       |
| --------------- | ------ | ------------------------------------------------- |
| `token_id`      | String | The Id of the NFT in the auction to be cancelled. |
| `token_address` | String | The address of the NFT contract.                  |

### PlaceBid

Places a bid for the auction for the given NFT Id. The bid must be sent as native funds along with this message. The previous largest bid gets automatically sent back to the bidder when they are outbid.

{% hint style="warning" %}
The following criteria must be met for the bid to be placed:

* The NFT is currently under auction
* The sender's bid is higher than the highest bid
* The sender does not currently hold the highest bid
* The sender is not the token owner
{% endhint %}

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
        "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                          |
| --------------- | ------ | ------------------------------------ |
| `token_id`      | String | The Id of the NFT to place a bid on. |
| `token_address` | String | The address of the NFT contract.     |

### Claim

Sends the winner of the auction the NFT and the funds to the NFT owner  when the auction has finished. Anyone is allowed to execute this message.

{% hint style="warning" %}
Can only be done when the `end_time` has been passed. If there were no bids placed, the original owner retains the token.
{% endhint %}

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
        "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `token_id`      | String | The Id of the token that was auctioned. |
| `token_address` | String | The address of the NFT contract.        |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### LatestAuctionState

Queries the most recent auction for the given token (either ongoing, complete, or not started yet).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(AuctionStateResponse)]
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
        "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                             |
| --------------- | ------ | ------------------------------------------------------- |
| `token_id`      | String | The Id of the NFT that we want to query the auction of. |
| `token_address` | String | The address of the NFT contract.                        |

#### AuctionStateResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AuctionStateResponse {
    pub start_time: Expiration,
    pub end_time: Expiration,
    pub high_bidder_addr: String,
    pub high_bidder_amount: Uint128,
    pub auction_id: Uint128,
    pub coin_denom: String,
    pub uses_cw20: bool,
    pub is_cancelled:bool, 
    pub min_bid: Option<Uint128>,
    pub owner: String,
    pub whitelist: Option<Vec<Addr>>,
    pub recipient: Option<Recipient>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_state_response": {
          "start_time": { "at_time": 1672764083954000000 },
          "end_time": { "at_time": 1672773083954000000 },
          "high_bidder_addr": "andr1...",
          "high_bidder_amount": "50",
          "auction_id": "0",
          "coin_denom":"uandr",
          "uses_cw20": false,
          "is_cancelled": false,
          "claimed": false,
          "min_bid":"300",
          "owner":"andr1...",
          "whitelist": ["andr1...", "andr1...", ...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                               | Description                                                           |
| -------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------- |
| `start_time`         | [Expiration](../platform-and-framework/common-types.md#expiration) | The start of the auction.                                             |
| `end_time`           | [Expiration](../platform-and-framework/common-types.md#expiration) | The end of the auction.                                               |
| `high_bidder_addr`   | String                                                             | The address of the highest bidder.                                    |
| `high_bidder_amount` | Uint128                                                            | The amount of the highest bid.                                        |
| `auction_id`         | Uint128                                                            | The Id of the auction.                                                |
| `coin_denom`         | String                                                             | The denom the auction is in.                                          |
| `is_cancelled`       | bool                                                               | Whether or not the auction has been cancelled.                        |
| `min_bid`            | Option\<Uint128>                                                   | The minimum bid that can be placed on the auctioned token.            |
| `whitelist`          | Option\<Vec\<Addr>>                                                | The whitelisted addresses if they were specified at time of creation. |
| `recipient`          | Option\<Recipient>                                                 | Address to receive the funds of the auction.                          |

### AuctionState

Gets the auction state for a particular `auction_id`.

{% hint style="warning" %}
Each Auction has an auction\_id which starts at 1 and increments every new auction.

To get the auction\_id of a particular token, use LatestAuctionState.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(AuctionStateResponse)]
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
        "auction_id": "1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type    | Description     |
| ------------ | ------- | --------------- |
| `auction_id` | Uint128 | The auction Id. |

#### Response

See [AuctionStateResponse](auction-v1.0.0.md#auctionstateresponse).

### Bids

Gets the bids for a given auction.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(BidsResponse)]
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
    "start_after": 700,
    "limit": 25
    }
 }
```
{% endtab %}
{% endtabs %}

| Name          | Type             | Description                                                                                            |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------ |
| `auction_id`  | Uint128          | The auction Id.                                                                                        |
| `start_after` | Option\<u64>     | Optional parameter to specify which bid to start after. If none specified index `0` will be used.      |
| `limit`       | Option\<u64>     | Optional parameter to specify how many bids to query. If none specified a default limit of 10 is used. |
| `order_by`    | Option\<OrderBy> | Optional parameter to specify the order of the bids being queries. Default is Ascending.               |

```rust
pub enum OrderBy {
    Asc,
    Desc,
}
```

#### BidsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
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
                "bidder": "andr1...",
                "amount": "500",
                "timestamp": "60"
            }
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type                              | Description         |
| ------ | --------------------------------- | ------------------- |
| `bids` | Vec<[Bid](auction-v1.0.0.md#bid)> | The retrieved bids. |

#### Bid

The state for a particular bid is stored in a basic struct.

```rust
pub struct Bid {
    pub bidder: String,
    pub amount: Uint128,
    pub timestamp: Timestamp,
}
```

| Name        | Type                                                             |                            |
| ----------- | ---------------------------------------------------------------- | -------------------------- |
| `bidder`    | String                                                           | The address of the bidder. |
| `amount`    | Uint128                                                          | The amount of funds bid.   |
| `timestamp` | [TimeStamp](../platform-and-framework/common-types.md#timestamp) | The time of the bid.       |

### AuctionIds

Queries the auction Ids for a given token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(AuctionIdsResponse)]
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
        "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                      |
| --------------- | ------ | -------------------------------- |
| `token_id`      | String | The Id of the token/NFT.         |
| `token_address` | String | The address of the NFT contract. |

#### AuctionIdsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AuctionIdsResponse {
    pub auction_ids: Vec<Uint128>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction_ids_response": {
        "auction_ids": ["1", "3", ... ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type          | Description      |
| ------------- | ------------- | ---------------- |
| `auction_ids` | Vec\<Uint128> | The auction Ids. |

### AuctionInfosForAddress

Gets all of the auction infos for a given token address.

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg {
    #[returns(AuctionInfo)]
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
       "token_address":"andr1...",
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
   "token_address":"andr1...",
   "token_id":"token_001"
   }
}
   
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="200.66666666666666">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>auction_ids</code></td><td>Vec&#x3C;Uint128></td><td>The Ids of the auctions that use  the specified <code>token_address</code></td></tr><tr><td><code>token_address</code></td><td>String</td><td>The address of the token contract.</td></tr><tr><td><code>token_id</code></td><td>String</td><td>The Id of the token that was auctioned.</td></tr></tbody></table>

### IsCancelled

Checks if the specified aution was cancelled.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 #[returns(bool)]
    IsCancelled {
        token_id: String,
        token_address: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_cancelled":{
   "token_id":"token_001",
   "token_address":"andr1..."
   }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `token_id`      | String | The Id of the token that was auctioned. |
| `token_address` | String | The address of the token contract.      |

Returns a true if the auction has been cancelled and false otherwise.

### IsClosed

Checks if the specified auction has been closes. Returns true only if the auction has been cancelled, the token has been claimed, or the end time has expired.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(bool)]
    IsClosed {
        token_id: String,
        token_address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_closed":{
   "token_id":"token_001",
   "token_address":"andr1..."
   }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `token_id`      | String | The Id of the token that was auctioned. |
| `token_address` | String | The address of the token contract.      |

Returns a true if the auction has been cancelled and false otherwise.

### IsClaimed

Checks if the NFT has been claimed after the auction has concluded.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(bool)]
    IsClaimed {
        token_id: String,
        token_address: String,
    }
}
```
{% endtab %}

{% tab title="JSON " %}
```json
{
"is_claimed":{
   "token_id":"token_001",
   "token_address":"andr1..."
   }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                             |
| --------------- | ------ | --------------------------------------- |
| `token_id`      | String | The Id of the token that was auctioned. |
| `token_address` | String | The address of the token contract.      |

Returns a true if the NFT has been claimed and false otherwise.

### AuthorizedAddresses

Gets all of the authorized CW721 addresses for the auction.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(AuthorizedAddressesResponse)]
    AuthorizedAddresses {
        start_after: Option<String>,
        limit: Option<u32>,
        order_by: Option<OrderBy>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorized_addresses":{
    "start_after":"andr1...",
    "limit":40,
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                         | Description                                                                                                                           |
| ------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `start_after` | Option\<String>                              | Optional parameter to specify which `address` to start from.                                                                          |
| `limit`       | Option\<u64>                                 | Optional parameter to specify how many addresses to return. If none specified a default limit of 25 is used. The maximum limit is 50. |
| `order_by`    | Option<[OrderBy](auction-v1.0.0.md#orderby)> | Whether to return the addresses in ascending or descending order. Defaults to ascending if not specified.                             |

#### OrderBy

How the returned addresses are ordered.

```rust
pub enum OrderBy {
    Asc,
    Desc,
}
```

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

[^1]: 
