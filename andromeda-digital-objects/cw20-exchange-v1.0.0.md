# CW20 Exchange V1.0.0

## Introduction

The **CW20 Exchange** ADO is used to sell CW20 tokens for other assets. The token to be sold is specified at instantiation, and then sales can be started on the token by the contract owner by sending them to this ADO. Each sale has an "asset" to purchase the tokens with. This asset can be:

* CW20&#x20;
* Native

Users can then purchase the CW20 token being sold by sending the required asset to the contract.

{% hint style="warning" %}
Multiple sales can take place at the same time but no two sales can have the same purchasing asset.
{% endhint %}

**ado\_type:** cw20-exchange

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub token_address: AndrAddr,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":"andr1...",
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                                           | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `token_address`  | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | Address of the CW20 token to be sold.                                                                                                                                                                                                                                                                                         |
| `kernel_address` | String                                                         | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String>                                                | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

## ExecuteMsg

### Receive

Receives CW20 tokens sent from the `token_address` contract by performing a  [Send](cw20-v1.0.0.md#send). These tokens can be set for sale or used to purchase other CW20 tokens. This depends on the attached hook message.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
        Receive(Cw20ReceiveMsg),
        }
```
{% endtab %}
{% endtabs %}

#### Cw20ReceiveMsg

```rust
pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}
```

The `msg` in the `Cw20ReceiveMsg`should be a base64 encoded `Cw20HookMsg`.

### Cw20HookMsg

{% hint style="warning" %}
The CW20 tokens sent with StartSale should come from the contract address specified at instantiation.

The CW20 tokens sent with Purchase should be the same as the defined Asset from StartSale message.
{% endhint %}

```rust
pub enum Cw20HookMsg {
    StartSale {
        asset: AssetInfo,
        exchange_rate: Uint128,
        recipient: Option<String>,
        start_time: Option<MillisecondsExpiration>,
        duration: Option<MillisecondsDuration>,
    },
    Purchase {
        recipient: Option<String>,
    },
}
```

### StartSale

{% hint style="warning" %}
Only available to the contract owner.

The asset to be traded cannot be the same as the asset being bought specified at instantiation.
{% endhint %}

Starts a sale on the CW20 tokens sent. This is attached as a `msg` when sending the CW20 tokens to the Exchange ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    StartSale {
        asset: AssetInfo,
        exchange_rate: Uint128,
        recipient: Option<String>,
        start_time: Option<MillisecondsExpiration>,
        duration: Option<MillisecondsDuration>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"start_sale":{
    "asset":{
        "native":"uandr"
        },
    "exchange_rate":"5",
    "start_time": 1663334970211,
    "duration": 900000
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type                                                                             | Description                                                                                                                                      |
| --------------- | -------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `asset`         | [AssetInfo](cw20-exchange-v1.0.0.md#undefined)                                   | The asset that may be used to purchase the token.                                                                                                |
| `exchange_rate` | Uint128                                                                          | The amount of the above `asset` required to purchase a single token.                                                                             |
| `recipient`     | Option\<String>                                                                  | The recipient of the sale proceeds. Defaults to the sender if not specified.                                                                     |
| `start_time`    | [MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds) | Optional start time in milliseconds since [epoch](https://www.epochconverter.com/clock). If not specified, then the sale will start immediately. |
| `duration`      | [MillisecondsDuration](../platform-and-framework/common-types.md#milliseconds)   | Optional duration for the sale in milliseconds from the `start_time`. If not specified then the sale never expires.                              |

#### AssetInfo

```rust
/// Represents an **asset info** instance containing only verified data;
///to be saved in contract storage
pub type AssetInfo = AssetInfoBase<Addr>;

pub enum AssetInfoBase<T> {
    Native(String),
    Cw20(T),
}

```

* **Native SDK coins:** To create an **asset info** instance of this type, provide the denomination
* **CW20 tokens:** To create an **asset info** instance of this type, provide the contract address

### Purchase

Purchase tokens using the CW20 tokens sent. This is attached as a `msg` when sending the CW20 tokens to the Exchange ADO.

{% hint style="warning" %}
You need to send an amount wich will get you a whole number in exchange. For example if the exchange rate is 5, then you need to send a multiple of 5 and nothing in between.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
     Purchase {
       recipient: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"purchase":{
    "recipient":"andr1..."
    }
} 
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                          |
| ----------- | --------------- | ------------------------------------------------------------------------------------ |
| `recipient` | Option\<String> | Optional recipient of the purchased CW20 tokens. If not set, defaults to the sender. |

***

## CancelSale

{% hint style="warning" %}
Only available for the contract owner.
{% endhint %}

Cancels an ongoing sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     CancelSale {
           asset: AssetInfo 
           }
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"cancel_sale":{
    "asset":{ 
        "cw20":"andr1..."
        }
    }
}
        
```
{% endtab %}
{% endtabs %}

| Name    | Type                                           | Description                                 |
| ------- | ---------------------------------------------- | ------------------------------------------- |
| `asset` | [AssetInfo](cw20-exchange-v1.0.0.md#assetinfo) | The purchasing asset of the sale to cancel. |

### Purchase

Purchase tokens with native funds.

{% hint style="warning" %}
Dont be confused by the previous [Purchase](cw20-exchange-v1.0.0.md#purchase) message. Although they share the same name, the first message is attached to a CW20 [Send](cw20-v1.0.0.md#send) and is used to buy using CW20 tokens while this message is called directly on the CW20 Exchange ADO and is used to buy using native funds.

You need to attach the native funds to the message when calling this Purchase.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Purchase {
         recipient: Option<String> 
         }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"purchase":{
    "recipient":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                                  |
| ----------- | --------------- | -------------------------------------------------------------------------------------------- |
| `recipient` | Option\<String> | Optional recipient to receive the purchased tokens. Defaults to the sender if not specified. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### Sale

Queries the sale info for the specified asset.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(SaleResponse)]
    Sale { asset: AssetInfo }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale":{
    "asset":{ 
        "cw20":"andr1..."    
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                           | Description                            |
| ------- | ---------------------------------------------- | -------------------------------------- |
| `asset` | [AssetInfo](cw20-exchange-v1.0.0.md#assetinfo) | The asset of the sale to get info for. |

#### SaleResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct SaleResponse {
    pub sale: Option<Sale>,
}
```
{% endtab %}
{% endtabs %}

| Name   | Type          | Description                 |
| ------ | ------------- | --------------------------- |
| `sale` | Option\<Sale> | The sale data if it exists. |

#### Sale

```rust
pub struct Sale {
    pub exchange_rate: Uint128,
    pub amount: Uint128,
    pub recipient: String,
    pub start_time: Expiration,
    pub end_time: Expiration,
    pub start_amount: Uint128,
}
```

| Name            | Type                                                               | Description                                                                   |
| --------------- | ------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| `exchange_rate` | Uint128                                                            | The amount of exchange asset needed to purchase one token.                    |
| `amount`        | Uint128                                                            | The amount of tokens for sale at the specified exchange\_rate.                |
| `recipient`     | String                                                             | The recipient of the sale proceeds.                                           |
| `start_time`    | [Expiration](../platform-and-framework/common-types.md#expiration) | The time when the sale starts.                                                |
| `end_time`      | [Expiration](../platform-and-framework/common-types.md#expiration) | The time when the sale ends.                                                  |
| `start_amount`  | Uint128                                                            | The amount of CW20 tokens for sale at the given rate at the start of the sale |

### TokenAddress

Queries the contract address of the token being purchased.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(TokenAddressResponse)]
    TokenAddress {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":{}
}
```
{% endtab %}
{% endtabs %}

#### TokenAddressResponse

The address of the token being sold.&#x20;

<pre class="language-rust"><code class="lang-rust">pub struct TokenAddressResponse {
    pub address: String,
<strong> }
</strong></code></pre>

### SaleAssets

Queries the different assets of the sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {    
    #[returns(SaleAssetsResponse)]
    SaleAssets {
        start_after: Option<String>,
        limit: Option<u32>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_assets":{
    "start_after":"token8",
    "limit": 67
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                             |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------- |
| `start_after` | Option\<String> | An optional address for which to start after, used for pagination.                                      |
| `limit`       | Option\<u32>    | Optional limit to the number of assets that are returned. Default limit is 50 and the max limit is 100. |

#### SaleAssetsResponse

A vec of string containing each of the assets.&#x20;

```rust
pub struct SaleAssetsResponse {
    pub assets: Vec<String>,
}
```

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
