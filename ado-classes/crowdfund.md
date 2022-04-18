---
description: >-
  A contract to allow the creation of a crowdfund for a token. A sale can be
  started by the owner to sell tokens from a specified token contract. The
  minting of tokens can be called by this contract.
---

# Crowdfund

## Introduction

As the name suggests, the **Crowdfund** ADO is a smart contract that allows users to start a crowdfund for their projects.&#x20;

The owner of the contract can start the crowdfund by executing `StartSale`. Before starting the sale, they can use the contract to mint the tokens from the defined `token_address` in the `InstantiateMsg`. In order to be eligible for the sale, the minter has to be defined as the crowdfund contract.&#x20;

Every sale sets a `min_tokens_sold` which specifies the minimum number of tokens that need to be sold in order for the sale to be considered successful which in turn acts as insurance for the buyers by allowing them to get a refund in case this goal was not achieved.&#x20;

The contract supports [modules](broken-reference) to extend it's functionality.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub token_address: AndrAddress,
    pub can_mint_after_sale: bool,
    pub modules: Option<Vec<Module>>,
    pub primitive_address: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":{
   "identifier":"terra1...",
   }
"primitive_address":"terra1..."
"can_mint_after_sale": true,
}
```
{% endtab %}
{% endtabs %}

| Name                  | Type                                                                        | Desctription                                                                                               |
| --------------------- | --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `token_address`       | [AndrAddress](../modules/module-definitions.md#andradress)                  | The contract address of the token.                                                                         |
| `modules`             | Option\<Vec<[Module](../modules/module-definitions.md#module-definitions)>> | Optional set of modules to attach to the contract.                                                         |
| `primitive_address`   | String                                                                      | The primitive contract address used to retrieve data.                                                      |
| `can_mint_after_sale` | bool                                                                        | A flag to whether minting is allowed after a sale has been done. Minting is never allowed during a sale.   |

## ExecuteMsg

### Mint

Mints a new token to be sold in a future sale. The message fields are defined and sent to the corresponding token contract defined as the `token_address` in the instantiation to mint. We use a vector of `MintMsg` to facilitate bulk minting for projects.

We allow for owners to be other than the contract incase the creator wants to set aside a few tokens for some other use, say airdrop, team allocation, etc... Only the tokens minted with the contract as the owner will be available to sell in the sale.

{% hint style="warning" %}
Only the contract owner can execute Mint.

Minting is only allowed before a sale starts.

The limit for the number of `MintMsg` that can be defined at once is 100.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 Mint(Vec<MintMsg<TokenExtension>>),
 }
```
{% endtab %}
{% endtabs %}

Minting definition is the same as the one defined in the NFT Collectible contract found [here](andromeda-digital-object.md#mint).

### StartSale

Initiates a new crowdfund with the specified information.

{% hint style="info" %}
Only available to the contract owner.

An Expiration must be set (cannot be never) and not in past date.

A sale should not be already ongoing.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
 StartSale {
        expiration: Expiration,
        price: Coin,
        min_tokens_sold: Uint128,
        max_amount_per_wallet: Option<Uint128>,
        recipient: Recipient,
    },
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"start_sale"{
 "expiration":{
  "at_height": 550,
  },
  "price":{
    "denom":"uusd",
    "amount":"10000"
    },
  "min_tokens_sold":"1000",
  "max_amount_per_wallet":"10",
  "recipient":{
   "addr":"terra1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                       | Description                                                       |
| ----------------------- | ------------------------------------------ | ----------------------------------------------------------------- |
| `exiration`             | [Expiration](../definitions/expiration.md) | When the sale ends.                                               |
| `price`                 | [Coin](../definitions/coin.md)             | The price per token.                                              |
| `min_tokens_sold`       | Uint128                                    | The minimum amount of tokens sold to go through with the sale.    |
| `max_amount_per_wallet` | Option\<Uint128>                           | The amount of tokens a wallet can purchase, default is 1.         |
| `recipient`             | [Recipient](../recipient.md)               | The recipient of the funds if the sale met the `min_tokens_sold`. |

### Purchase

Purchases the token with the specified `token_id`.

{% hint style="info" %}
A sale needs to be in progress in order to purchase a token.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
 Purchase {
        token_id: String,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"purchase":{
  "token_id":"atoken",
    }
}
```
{% endtab %}
{% endtabs %}

### ClaimRefund

If the minimum number of tokens to be sold was not reached. The user can claim their own refund. Refunding will return the funds to the buyer and burn the token.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg{
  ClaimRefund {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_refund":{}
}
```
{% endtab %}
{% endtabs %}

### EndSale

Ends the sale. In the case that the minimum sold is not achieved, refunds are sent to the buyers and tokens are burnt. If the minimum sold was achieved, the tokens are sent to the buyers and the funds to the recipient.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
   EndSale {
        limit: Option<u32>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"end_sale":{
  "limit": 200,
  }
 }
```
{% endtab %}
{% endtabs %}

| Name    | Type          | Description                                                                                                                                                                        |
| ------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `limit` | Option\<u32>> | An optional limit on the number of transferred tokens in case the sale was a success, or the number of refunds to issue in case the sale did not succeed (min amount not reached). |

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### State

Queries the state of the sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    State {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"state":{}
}
```
{% endtab %}
{% endtabs %}

#### The response is stored in a State struct:

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct State {
    pub expiration: Expiration,
    pub price: Coin,
    pub min_tokens_sold: Uint128,
    pub max_amount_per_wallet: Uint128,
    pub amount_sold: Uint128,
    pub amount_to_send: Uint128, 
    pub amount_transferred: Uint128,
    pub recipient: Recipient,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "expiration":{
  "at_height": 550,
  },
  "price":{
    "denom":"uusd",
    "amount":"10000"
    },
  "min_tokens_sold":"1000",
  "max_amount_per_wallet":"10",
  "amount_sold":"600",
  "amount_to_send":"100000",
  "amount_transferred":"300",
  "recipient":{
  "addr":"terra1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                       | Description                                                                                                           |
| ----------------------- | ------------------------------------------ | --------------------------------------------------------------------------------------------------------------------- |
| `expiration`            | [Expiration](../definitions/expiration.md) | The expiration denoting when the sale ends.                                                                           |
| `price`                 | [Coin](../definitions/coin.md)             | The price of each token.                                                                                              |
| `min_tokens_sold`       | Uint128                                    | The minimum number of tokens sold for the sale to go through.                                                         |
| `max_amount_per_wallet` | Uint128                                    | The max number of tokens allowed per wallet.                                                                          |
| `amount_sold`           | Uint128                                    | Number of tokens sold.                                                                                                |
| `amount_to_send`        | Uint128                                    | The amount of funds to send to recipient if sale successful. This already takes into account the royalties and taxes. |
| `amount_transferred`    | Uint128                                    | Number of tokens transferred to purchasers if sale was successful.                                                    |
| `recipient`             | [Recipient](../recipient.md)               | The recipient of the raised funds if the sale is successful.                                                          |

### Config

Queries the configuration of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    Config {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"config":{}
}
```
{% endtab %}
{% endtabs %}

#### The response is stored in a Config struct:

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct Config {
    pub token_address: Addr,
    pub can_mint_after_sale: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":"terra1...",
"can_mint_after_sale": true
}
```
{% endtab %}
{% endtabs %}

| Name                  | Type | Description                                                                            |
| --------------------- | ---- | -------------------------------------------------------------------------------------- |
| `token_address`       | Addr | The address of the token contract whose tokens are being sold.                         |
| `can_mint_after_sale` | bool | Whether or not the owner can mint additional tokens after the sale has been conducted. |

### AndrQuery

Check[ AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
