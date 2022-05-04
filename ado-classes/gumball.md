# Gumball

## Introduction

The **Gumball** ADO is a smart contract that allows users to buy an NFT randomly from a collection.

The contract can be in 2 different modes:

**Buying mode:** Where people can buy the NFTs from the gumball contract and minting is halted.

**Minting mode:** Where owners can mint new NFTs for the gumball contract and buying is halted.

Upon instantiation, the contract is set to minting mode. To change the mode, we execute `SwitchStatus`.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub andromeda_cw721_contract: AndrAddress,
    pub randomness_source: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andromeda_cw721_contract":{
    "identifier":"terra1..."
    },
"randomness_source":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                       | Type                                                    | Description                                                               |
| -------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------- |
| `andromeda_cw721_contract` | [AndrAddress](../common-types/recipient.md#andraddress) | The contract address of the NFT contract.                                 |
| `randomness_source`        | String                                                  | The contract address for source of randomness. (Currently using Terrand). |

## ExecuteMsg

### Mint

Mints new tokens from the specified `andromeda_cw721_contract` to be sold in the gumball contract.

{% hint style="warning" %}
The owner of the tokens should be set as the Gumball contract to be eligible for selling.

More than one NFT can be minted in a single message.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Mint(Vec<GumballMintMsg>),
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "mint": [{
        "token_id": "anewtoken",
        "owner": "terra1...",
        "extension":{
         "name":"mytoken",
         "publisher":"publisher",
         "description":"This token ....",
         "archived": false
        },
        ...
        ]
    }

```
{% endtab %}
{% endtabs %}

#### GumballMintMsg

```rust
pub struct GumballMintMsg {
    pub token_id: String,
    pub owner: Option<String>,
    pub token_uri: Option<String>,
    pub extension: TokenExtension,

```

Same as the [MintMsg ](andromeda-digital-object.md#mint)in NFT collectible contract.

### Buy

Buy one random NFT.

{% hint style="warning" %}
The gumball status needs to be in buy mode. (Status set to true).

Only one type of funds should be sent which is "uusd" for now.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
  Buy {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"buy":{}
}
```
{% endtab %}
{% endtabs %}

### SetSaleDetails

Sets the price of the each token, the maximum amount of tokens that a single address can buy, and the recipient of the funds of the sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
       SetSaleDetails {
        price: Coin,
        max_amount_per_wallet: Option<Uint128>,
        recipient: Recipient,
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"set_sale_details":{
    "price":{
        "denom":"uusd",
        "amount":"1000000"
        },
    "max_amount_per_wallet":"50",
    "recipient":{
        "addr":"terra1..."
        }
}   
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                      | Description                                                |
| ----------------------- | ----------------------------------------- | ---------------------------------------------------------- |
| `price`                 | [Coin](../common-types/coin.md)           | The price of the token.                                    |
| `max_amount_per_wallet` | Option\<Uint128>                          | The amount of tokens a wallet can purchase, defaults to 1. |
| `recipient`             | [Recipient](../common-types/recipient.md) | The recipient of the funds.                                |

### SwitchStatus

Automatically switches to opposite status. True means buying is allowed and minting is halted. False means the opposite. The status starts as false.

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg {
    SwitchStatus {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"switch_status":{}
}
```
{% endtab %}
{% endtabs %}

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### NumberOfNfts

Queries the number of tokens currently available for purchase.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    NumberOfNfts {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"number_of_nfts":{}
}
```
{% endtab %}
{% endtabs %}

#### NumberOfNftsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct NumberOfNFTsResponse {
    pub number: usize,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"number": 5
}
```
{% endtab %}
{% endtabs %}

| Name     | Type  | Description                                  |
| -------- | ----- | -------------------------------------------- |
| `number` | usize | The number of tokens available for purchase. |

### SaleDetails

Gets the sale details which are set by executing `SetSaleDetails`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    SaleDetails {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sale_details":{}
}
```
{% endtab %}
{% endtabs %}

#### SaleDetailsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct State {
    pub price: Coin,
    pub max_amount_per_wallet: Uint128,
    pub recipient: Recipient,
}

```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "price":{
        "denom":"uusd",
        "amount":"1000000"
        },
    "max_amount_per_wallet":"50",
    "recipient":{
        "addr":"terr1..."
        }
}  
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                      | Description                                 |
| ----------------------- | ----------------------------------------- | ------------------------------------------- |
| `price`                 | [Coin](../common-types/coin.md)           | The price of each token.                    |
| `max_amount_per_wallet` | Uint128                                   | The max number o tokens allowed per wallet. |
| `recipient`             | [Recipient](../common-types/recipient.md) | The recipient of the funds upon sale.       |

### Status

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    Status {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"status":{}
}
```
{% endtab %}
{% endtabs %}

#### StatusResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct StatusResponse {
    pub status: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"status": true
}
```
{% endtab %}
{% endtabs %}

| Name   | Type | Description                                                     |
| ------ | ---- | --------------------------------------------------------------- |
| status | bool | True if in buying mode. False if minting mode. Starts as false. |

### AndrQuery

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
