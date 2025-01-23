# Marketplace

## Introduction

The **Marketplace** ADO is a smart contract that allows you to sell your NFTs in a marketplace. The seller sends their NFT to the Marketplace ADO and attaches the sale options such as the price and funds used to purchase the NFT. Once the NFT is sent, the sale will start at the time specified by the seller.

Purchasing the NFT can be customized to work with one of the following options:

* **Native:** By specifying the denom of the chain in the [StartSale](marketplace.md#start-sale) message.&#x20;
* **CW20:** By specifying the contract address of the CW20 token to be used in the StartSale. The CW20 tokens allowed to be set as the purchasing token can be restricted by specifying `authorized_cw20_address` at instantiation. If this is not specified, then any CW20 token can be set.

{% hint style="warning" %}
&#x20;Each sale is assigned an Id which starts at 1 and increments for each new sale.&#x20;
{% endhint %}

**Ado\_type:** marketplace

**Version: 2.3.0**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub authorized_cw20_address: Option<AndrAddr>,
    pub authorized_token_addresses: Option<Vec<AndrAddr>>,
    pub kernel_address: String,
    pub owner: Option<String>

}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorized_cw20_address":"andr1...",
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="306">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>authorized_cw20_address</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#andraddr">AndrAddr</a>></td><td>Optional CW20 address to authorize to be used as the purchasing coin for the NFTs in the marketplace sale. If not specified, then any CW20 can be set as the purchasing coin.</td></tr><tr><td><code>authorized_token_addresses</code></td><td>Option&#x3C;Vec&#x3C;<a href="../platform-and-framework/common-types.md#andraddr">AndrAddr</a>></td><td>Optional set of CW721 contract addresses to be allowed to send NFTs to the Marketplace ADO. If not specified, then any CW721 can send NFTs to the marketplace to be sold.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts.md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified. </td></tr></tbody></table>

## ExecuteMsg

### ReceiveNft

Receives a token from a [`SendNft`](cw721.md#sendnft) and starts an auction based on the given parameters in the **StartSale** struct. &#x20;

{% hint style="warning" %}
This message is not called by the user on this ADO, but is the case that handles receiving NFTs from a CW721 ADO.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
  ReceiveNft(Cw721ReceiveMsg)
}
```
{% endtab %}
{% endtabs %}

#### Cw721ReceiveMsg

```rust
pub struct Cw721ReceiveMsg {
    pub sender: String,
    pub token_id: String,
    pub msg: Binary,
}
```

The `msg` in the `Cw721ReceiveMsg` should be a base64 encoded binary of a  `Cw721HookMsg`.

### Start Sale&#x20;

A CW721 hook message that starts a new sale with the given parameters.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw721HookMsg {
    StartSale {
     price: Uint128,
     coin_denom: Asset,
     start_time: Option<Expiry>,
     duration: Option<MillisecondsDuration>,
     recipient: Option<Recipient>,
       }
   }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "start_sale": {
          "price":"500000",
          "coin_denom":{
                "native_token":"uandr"
                },
          "start_time":{
                "from_now":"7200000"
                },
          "duration": 60000000,
          "recipient":{
                "address":"andr1..."
                }
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th>Name</th><th width="267">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>price</code></td><td>Uint128</td><td>The price of the NFT, which is the amount of <code>coin_denom</code> needed to buy the NFT.</td></tr><tr><td><code>coin_denom</code></td><td><a href="../platform-and-framework/common-types.md#asset">Asset</a></td><td>The denom to be used to buy the NFT. Can be either a CW20 or native funds. For CW20, provide the contract address e.g. "<strong>andr1...</strong>". For native provide the denom e.g. "<strong>uandr</strong>".</td></tr><tr><td><code>start_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#expiry">Expiry</a>></td><td>Optional start time in milliseconds since <a href="https://www.epochconverter.com/clock">epoch</a>. If not specified, then the sale will start immediately.</td></tr><tr><td><code>duration</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsDuration</a>></td><td>Optional duration for the sale in milliseconds from the <code>start_time</code>. If not specified then the sale never expires.</td></tr><tr><td><code>uses_cw20</code></td><td>bool</td><td>Whether a CW20 token is used to purchase the NFT or not.</td></tr><tr><td><code>recipient</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#recipient">Recipient</a>></td><td>Optional address to receive the funds from the NFT sale. If not specified, then the funds will go to the sender of the NFT.</td></tr></tbody></table>

***

### Receive

Receives tokens from a CW20 [Send](cw20.md#send)  message to be used to buy an NFT.

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
In order to buy and NFT using a CW20, you need to define the message of the `Cw20ReceiveMsg` as a `Cw721HookMsg`.
{% endhint %}

### Buy (CW20)

Buys an NFT using the sent CW20 tokens.

{% hint style="warning" %}
You need to get the base64 encoded representation of the JSON message and attach it as the `msg` field for the CW20 **Send** message.

The Owner of the NFT is not allowed to buy it.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    Buy {
        token_id: String,
        token_address: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"buy":{
    "token_id":"1",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                         |
| --------------- | ------ | --------------------------------------------------- |
| `token_id`      | String | The token id of the NFT you want to buy.            |
| `token_address` | String | The address of the NFT contract the NFT belongs to. |

***

### UpdateSale

Update the price and denom for the sale of the token.

{% hint style="warning" %}
Only available to the NFT owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg {
</strong>    UpdateSale {
        token_id: String,
        token_address: String,
        price: Uint128,
        coin_denom: Asset,
        recipient:Option&#x3C;Recipient>
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_sale":{
    "token_id":"1",
    "token_address":"andr1...",
    "price":"100",
    "coin_denom":{
        "native_token":"native"
        },
    "recipient":{
        "address":"andr1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type                                                                     | Description                                                                                                                                                                            |
| --------------- | ------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token_id`      | String                                                                   | The Id of the token to update the sale for.                                                                                                                                            |
| `token_address` | String                                                                   | The address of the cw721 contract that minted the token.                                                                                                                               |
| `price`         | Uint128                                                                  | The price of the NFT, which is the amount of `coin_denom` needed to buy the NFT.                                                                                                       |
| `coin_denom`    | [Asset](../platform-and-framework/common-types.md#asset)                 | The denom to be used to buy the NFT. Can be either a CW20 or native funds. For CW20, provide the contract address e.g. "**andr1...**". For native provide the denom e.g. "**uandr**".  |
| `recipient`     | Option<[Recipient](../platform-and-framework/common-types.md#recipient)> | Optional address to receive the funds from the NFT sale. If not specified, then the funds will go to the sender of the NFT.                                                            |

### Buy

Buys the NFT that is for sale.

{% hint style="warning" %}
Dont forget to attach the required funds.

The Owner of the NFT is not allowed to buy it.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg { 
</strong> Buy {
        token_id: String,
        token_address: String,
     }
  }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"buy":{
    "token_id":"1",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                          |
| --------------- | ------ | ---------------------------------------------------- |
| `token_id`      | String | The Id of the NFT to buy.                            |
| `token_address` | String | The address of the cw721 that minted the NFT to buy. |

### AuthorizeContract

Authorize a CW721 or CW20 contract to send tokens to this ADO.&#x20;

{% hint style="warning" %}
Only available to the contract owner.

CW721 is used to send NFTs to be sold, and CW20 is used to send tokens to be used in sales.

For CW721, the action should be specified as "send\_nft".

For CW20, the action should be specified as "send\_cw20".
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  AuthorizeContract {
        action: PermissionAction,
        addr: AndrAddr,
        expiration: Option<Expiry>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorize_contract":{
    "action":"send_cw20",
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                               | Description                                                    |
| ------------ | ------------------------------------------------------------------ | -------------------------------------------------------------- |
| `action`     | [PermissionAction](marketplace.md#permissionaction)                | The action to authorize the specified address to do.           |
| `addr`       | [AndrAddr](../platform-and-framework/common-types.md#andraddr)     | The contract address of the CW721 (NFT) contract to authorize. |
| `expiration` | Option<[Expiry](../platform-and-framework/common-types.md#expiry)> | An optional expiration for the permission.                     |

#### PermissionAction

```rust
#[cw_serde]
pub enum PermissionAction {
    SendCw20,
    SendNft,
}
```

**SendCw20:** Used in case the ADO to authorize/deauthorize is a CW20 ADO.&#x20;

**SendNFT:** Used in case the ADO to authorize/deauthorize is a CW721 ADO.

### DeauthorizeContract

Removes authorization from a CW721 or CW20 contract to send tokens to this ADO.

{% hint style="warning" %}
Only available to the contract owner.

For CW721, the action should be specified as "send\_nft".

For CW20, the action should be specified as "send\_cw20".
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 DeauthorizeContract {
        action: PermissionAction,
        addr: AndrAddr,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deauthorize_contract":{
    "action":"send_nft",
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                                           | Description                                                                   |
| -------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| `action` | [PermissionAction](marketplace.md#permissionaction)            | The action to deauthorize the specified address to do.                        |
| `addr`   | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The contract address of the CW721 (NFT) contract to remove authorization for. |

### CancelSale

Cancels the sale for the specified NFT.

{% hint style="warning" %}
Only the NFT owner can cancel the sale.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    CancelSale {
        token_id: String,
        token_address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"cancel_sale":{
    "token_id":"1",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                                          |
| --------------- | ------ | -------------------------------------------------------------------- |
| `token_id`      | String | The Id of the NFT to cancel the sale for.                            |
| `token_address` | String | The address of the cw721 that minted the NFT to cancel the sale for. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### LatestSaleState

Gets the latest sale state for the given token. This will either be the current sale if there is one in progress or the last completed one.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg {
 #[returns(SaleStateResponse)]
 LatestSaleState {
        token_id: String,
        token_address: String,
     }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"latest_sale_state":{
    "token_id":"1",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                            |
| --------------- | ------ | ------------------------------------------------------ |
| `token_id`      | String | The Id of the token to check.                          |
| `token_address` | String | The address of the cw721 that minted the NFT to check. |

#### SaleStateResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct SaleStateResponse {
    pub sale_id: Uint128,
    pub coin_denom: String,
    pub price: Uint128,
    pub status: Status,
    pub start_time: Expiration,
    pub end_time: Expiration,
    pub recipient: Option<Recipient>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_id":"3",
"coin_denom":"uandr",
"price":"100",
"status":"open",
"start_time":{
    "expiration":{
        "at_time":"1146593483949835832"
    }
 },
"end_time":{
    "expiration":{
           "at_time":"1246593483949835832"
    } 
  }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                     | Description                                                                                                                                                                                               |
| ------------ | ------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sale_id`    | Uint128                                                                  | The Id of the sale. The first sale has an Id of 1 and each sale after it increments the Id by 1.                                                                                                          |
| `coin_denom` | String                                                                   | The denom used in the sale.                                                                                                                                                                               |
| `price`      | Uint128                                                                  | The price of the NFT.                                                                                                                                                                                     |
| `status`     | Status                                                                   | <p>The status of the sale which can be one of the following options:</p><p>-<strong>Open</strong></p><p>-<strong>Expired</strong></p><p>-<strong>Executed</strong></p><p>-<strong>Cancelled</strong> </p> |
| `start_time` | [Expiration](../platform-and-framework/common-types.md#expiration)       | The time the sale on the NFT will start.                                                                                                                                                                  |
| `end_time`   | [Expiration](../platform-and-framework/common-types.md#expiration)       | The time the the sale on the NFT will end.                                                                                                                                                                |
| `recipient`  | Option<[Recipient](../platform-and-framework/common-types.md#recipient)> | The address that will receive the funds from the sale of the NFT.                                                                                                                                         |

### SaleState

Gets the sale state for the given sale Id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg
#[returns(SaleStateResponse)]
 SaleState {
        sale_id: Uint128,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_state":{
    "sale_id":"1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type    | Description                  |
| --------- | ------- | ---------------------------- |
| `sale_id` | Uint128 | The Id of the sale to check. |

Returns a [SaleStateResponse](marketplace.md#salestateresponse).

### SaleIds

Queries the sale Ids of the specified token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(SaleIdsResponse)]
     SaleIds {
        token_id: String,
        token_address: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_ids":{
    "token_id":"1",
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                   |
| --------------- | ------ | --------------------------------------------- |
| `token_id`      | String | The Id of the token to check the sale for.    |
| `token_address` | String | The address of the cw721 that minted the NFT. |

Returns the Ids of the sales that have been conducted on the NFT.&#x20;

### SaleInfosForAddress

Get sale information on the provided CW721 token address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 #[returns(Vec<SaleInfo>)]
 SaleInfosForAddress {
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
"sale_infos_for_address":{
    "token_address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type            | Description                                                                                                                           |
| --------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `token_address` | String          | The cw721  contract address to check the sales for.                                                                                   |
| `start_after`   | Option\<String> | Optional parameter to specify which `SaleInfo` to start from. If none specified index `0` will be used.                               |
| `limit`         | Option\<u64>    | Optional parameter to specify how many `SaleInfo` to query. If none specified a default limit of 10 is used. The maximum limit is 30. |

#### SaleInfo

A `Vec<SaleInfo>` is returned.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct SaleInfo {
    pub sale_ids: Vec<Uint128>,
    pub token_address: String,
    pub token_id: String,
}
```
{% endtab %}
{% endtabs %}

| Name            | Type          | Description                                |
| --------------- | ------------- | ------------------------------------------ |
| `sale_ids`      | Vec\<Uint128> | The Ids of the sales conducted on the NFT. |
| `token_address` | String        | The cw721  contract address.               |
| `token_id`      | String        | The Id of the token.                       |

### AuthorizedAddresses

Gets all of the authorized ADO addresses for the specified action.

{% hint style="warning" %}
For CW721, the action should be specified as "send\_nft".

For CW20, the action should be specified as "send\_cw20".
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 #[returns(::andromeda_std::common::denom::AuthorizedAddressesResponse)]
    AuthorizedAddresses {
        action: PermissionAction,
        start_after: Option<String>,
        limit: Option<u32>,
        order_by: Option<OrderBy>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorized_addresses":{
    "action":"send_nft",
    "start_after":"andr1...",
    "limit":40,
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                | Description                                                                                                                           |
| ------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `action`      | [PermissionAction](marketplace.md#permissionaction) | The type of authorized addresses to fetch.                                                                                            |
| `start_after` | Option\<String>                                     | Optional parameter to specify which `address` to start from.                                                                          |
| `limit`       | Option\<u64>                                        | Optional parameter to specify how many addresses to return. If none specified a default limit of 25 is used. The maximum limit is 50. |
| `order_by`    | Option<[OrderBy](marketplace.md#orderby)>           | Whether to return the addresses in ascending or descending order. Defaults to ascending if not specified.                             |

#### OrderBy

How the returned addresses are ordered.

```rust
pub enum OrderBy {
    Asc,
    Desc,
}
```

#### AuthorizedAddressesResponse

```rust
#[cw_serde]
pub struct AuthorizedAddressesResponse {
    pub addresses: Vec<String>,
}
```

| Name        | Type         | Description                                                                                |
| ----------- | ------------ | ------------------------------------------------------------------------------------------ |
| `addresses` | Vec\<String> | A vector containing the contract addresses of the authorized ADO for the specified action. |

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
