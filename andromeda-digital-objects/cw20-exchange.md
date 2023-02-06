# CW20 Exchange

## Introduction

The **CW20 Exchange** ADO is used to sell CW20 tokens for other assets. The token to be sold is specified at instantiation and then sales can be started on the token by the contract owner. Each sale has an "asset" to purchase the tokens with. This asset can be:

* CW20&#x20;
* Native

Users can then purchase the CW20 token being sold by sending the required asset to the contract.

{% hint style="warning" %}
Multiple sales can take place at the same time but no two sales can have the same purchasing asset.
{% endhint %}

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub token_address: AndrAddress,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":{
    "identifier":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type                                                                 | Description                                    |
| --------------- | -------------------------------------------------------------------- | ---------------------------------------------- |
| `token_address` | [AndrAddress](../platform-and-framework/common-types.md#andraddress) | Contract address of the CW20 token to be sold. |

## ExecuteMsg

### Receive

Receives CW20 tokens sent from the `token_address` contract by performing a  [Send](cw20.md#send). These tokens can be set for sale or used to purchase other CW20 tokens. This depends on the attached hook message.

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

#### StartSale

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

Starts a sale on the CW20 tokens sent.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    StartSale {
        asset: AssetInfo,
        exchange_rate: Uint128,
        recipient: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"start_sale":{
    "asset":{
        "asset_info":{
              "cw20":"andr1..."
              }
        },
    "exchange_rate":"5"
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type                                    | Description                                                                  |
| --------------- | --------------------------------------- | ---------------------------------------------------------------------------- |
| `asset`         | [AssetInfo](cw20-exchange.md#undefined) | The asset that may be used to purchase the token.                            |
| `exchange_rate` | Uint128                                 | The amount of the above `asset` required to purchase a single token.         |
| `recipient`     | Option\<String>                         | The recipient of the sale proceeds. Defaults to the sender if not specified. |

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

#### Purchase

Purchase tokens using the CW20 tokens sent.

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

### CancelSale

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
        "asset_info":{
              "cw20":"andr1..."
              }
        }
    }
}
        
```
{% endtab %}
{% endtabs %}

| Name    | Type                                    | Description                                 |
| ------- | --------------------------------------- | ------------------------------------------- |
| `asset` | [AssetInfo](cw20-exchange.md#assetinfo) | The purchasing asset of the sale to cancel. |

### Purchase

Purchase tokens with native funds.

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

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

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
        "asset_info":{
              "cw20":"andr1..."
              }
        }
  }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                    | Description                            |
| ------- | --------------------------------------- | -------------------------------------- |
| `asset` | [AssetInfo](cw20-exchange.md#assetinfo) | The asset of the sale to get info for. |

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
}
```

| Name            | Type    | Description                                                    |
| --------------- | ------- | -------------------------------------------------------------- |
| `exchange_rate` | Uint128 | The amount of exchange asset needed to purchase one token.     |
| `amount`        | Uint128 | The amount of tokens for sale at the specified exchange\_rate. |
| `recipient`     | String  | The recipient of the sale proceeds.                            |

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
    "limit": 70
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

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).