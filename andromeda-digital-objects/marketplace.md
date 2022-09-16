# Marketplace

## Introduction

The **Marketplace** ADO is a smart contract that allows you to sell your NFTs in a marketplace. The seller sends their NFT to the ADO with a custom price and denomination to be used to buy the NFT. Once the NFT is sent, it is up for sale and buyers can pay the price to buy the NFT.

{% hint style="warning" %}
&#x20;Each sale is assigned an Id which starts at 1 and increments for each new sale. .
{% endhint %}

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub modules: Option<Vec<Module>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"modules":[
    {
    "module_type":"address-list",
    "address":{
        "identifier":"juno1..."
        },
    "is_mutable": true
    }
  ]
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                  | Description                                                                                                                            |
| --------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `modules` | Option\<Vec\<Module>> | An optional vector of Andromeda [Modules](../modules/module-definitions.md). "rates", "address\_list", "receipt" modules can be added. |

## ExecuteMsg

### ReceiveNft

Receives a token from a [`SendNft`](andromeda-digital-object.md#sendnft) and starts an auction based on the given parameters in the StartSale struct. &#x20;

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

#### Cw721HookMsg

Starts a new sale with the given parameters.&#x20;

```rust
pub enum Cw721HookMsg {
    StartSale { price: Uint128, coin_denom: String },
   }
```

| Name         | Type    | Description                       |
| ------------ | ------- | --------------------------------- |
| `price`      | Uint128 | The price of the NFT.             |
| `coin_denom` | String  | The denom to pay the price with.  |

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
        coin_denom: String,
    }
}</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_sale":{
    "token_id":"1",
    "token_address":"juno1...",
    "price":"100",
    "coin_denom":"juno"
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type    | Description                                              |
| --------------- | ------- | -------------------------------------------------------- |
| `token_id`      | String  | The id of the token to update the sale for.              |
| `token_address` | String  | The address of the cw721 contract that minted the token. |
| `price`         | Uint128 | The price of the NFT.                                    |
| `coin_denom`    | String  | The denomination used to buy the NFT.                    |

### Buy

Buys the NFT that is for sale.

{% hint style="warning" %}
Dont forget to attach the required funds.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg { 
</strong> Buy {
        token_id: String,
        token_address: String,
     }
  }</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"buy":{
    "token_id":"1",
    "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                          |
| --------------- | ------ | ---------------------------------------------------- |
| `token_id`      | String | The id of the NFT to buy.                            |
| `token_address` | String | The address of the cw721 that minted the NFT to buy. |

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
    "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                                          |
| --------------- | ------ | -------------------------------------------------------------------- |
| `token_id`      | String | The id of the NFT to cancel the sale for.                            |
| `token_address` | String | The address of the cw721 that minted the NFT to cancel the sale for. |

### AndrReceive

{% hint style="info" %}
Has the modules feature enabled.
{% endhint %}

Check [AndrReceive](../platform-and-framework/ado\_base.md).

## QueryMsg

### LatestSaleState

Gets the latest sale state for the given token. This will either be the current sale if there is one in progress or the last completed one.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg {
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
    "token_address":"juno1..."
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
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_id":"3",
"coin_denom":"juno",
"price":"100",
"status":"open"
}
```
{% endtab %}
{% endtabs %}

| Name         | Type    | Description                                                                                                    |
| ------------ | ------- | -------------------------------------------------------------------------------------------------------------- |
| `sale_id`    | Uint128 | The id of the sale. The first sale has an Id of 1 and each sale after it increments the Id by 1.               |
| `coin_denom` | String  | The denom used in the sale.                                                                                    |
| `price`      | Uint128 | The price of the NFT.                                                                                          |
| `status`     | Status  | <p>The status of the sale which can be one of three options:</p><p>-Open</p><p>-Executed</p><p>-Cancelled </p> |

### SaleState

Gets the sale state for the given sale id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg
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
| `sale_id` | Uint128 | The id of the sale to check. |

Returns a [SaleStateResponse](marketplace.md#salestateresponse).

### SaleIds

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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
    "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type   | Description                                   |
| --------------- | ------ | --------------------------------------------- |
| `token_id`      | String | The Id of the token to check the sale for.    |
| `token_address` | String | The address of the cw721 that minted the NFT. |

Returns the ID of the sale.&#x20;

### SaleInfosForAddress

Get sale information on the provided cw721 token address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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
    "token_address":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type            | Description                                                                                                                           |
| --------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `token_address` | String          | The cw721 address to check the sales for.                                                                                             |
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

| Name            | Type          | Description          |
| --------------- | ------------- | -------------------- |
| `sale_ids`      | Vec\<Uint128> | The id of the sale.  |
| `token_address` | String        | The cw721 address.   |
| `token_id`      | String        | The id of the token. |

### AndrQuery

Check [AndrQuery](../platform-and-framework/ado\_base.md#andrquery).
