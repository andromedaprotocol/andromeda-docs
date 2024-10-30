# Crowdfund V1.0.0

## Introduction

As the name suggests, the **Crowdfund** ADO is a smart contract that allows users to start a crowdfund for their projects by selling NFTs.

The owner of the contract can start the crowdfund by executing `StartSale`. Before starting the sale, they can use the contract to mint the tokens from the specified `token_address` in the `InstantiateMsg`. In order to be eligible for the sale, the NFT needs to be minted and owned by the crowdfund ADO.&#x20;

We allow for owners other than the contract, incase the creator wants to set aside a few  tokens for some other use, say airdrop, team allocation, etc., but only those which have the contract as the owner will be available to sell.

Every sale sets a `min_tokens_sold` which specifies the minimum number of tokens that need to be sold in order for the sale to be considered successful. This acts as an insurance for the buyers by allowing them to get a refund in case this goal was not achieved. If this threshhold is met, the owner can end the sale.

**Ado\_type**: crowdfund

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub token_address: AndrAddr,
    pub can_mint_after_sale: bool,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":"andr1...",
"can_mint_after_sale": true,
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="259.3333333333333">Name</th><th width="212.3589484327604">Type</th><th>Desctription</th></tr></thead><tbody><tr><td><code>token_address</code></td><td><a href="../platform-and-framework/common-types.md#andraddr">AndrAddr</a></td><td>The reference to the CW721 ADO that will mint the NFTs for the sale. Can be either the contract address or a VFS reference.</td></tr><tr><td><code>can_mint_after_sale</code></td><td>bool</td><td>A flag to whether minting is allowed after a sale has been done. Minting is never allowed during a sale.  </td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

## ExecuteMsg

### Mint

Mints a new token to be sold in a future sale. The message fields are defined and sent to the corresponding token contract defined as the `token_address` in the instantiation to mint. We use a vector of `MintMsg` to facilitate bulk minting for projects.

We allow for owners to be other than the contract incase the creator wants to set aside a few tokens for some other use, say airdrop, team allocation, etc... Only the tokens minted with the contract as the owner will be available to sell in the sale.

{% hint style="warning" %}
Only the contract owner can execute Mint.

Minting is only allowed before a sale starts.

The limit for the number of `MintMsg` is 100.

Unlike other mint messages, the crowdfund mint allows users to keep the owner field empty defaulting the owner to the crowdfund ADO, making it eligible for a sale.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 Mint(Vec<CrowdfundMintMsg>),
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"mint":[
 {
 "token_id":"myid",
 "owner":"andr1...",
 "token_uri":"https://gateway.pinata.cloud/ipfs...",
  "extension": {
            "publisher": "Andromeda"
        }
     }
  ]
 }
```
{% endtab %}
{% endtabs %}

#### CrowdfundMintMsg

```rust
pub struct CrowdfundMintMsg {
    pub token_id: String,
    pub owner: Option<String>,
    pub token_uri: Option<String>,
    pub extension: TokenExtension,
}
```

| Name        | Type                                             | Description                                                                                                                                    |
| ----------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `token_id`  | String                                           | Unique Id of the NFT.                                                                                                                          |
| `owner`     | Option\<String>                                  | The owner of the newly minted NFT. In order to be included in the sale, the owner field should be kept empty or set as the crowdfund contract. |
| `token_uri` | Option\<String>                                  |  Universal resource identifier for this NFT  Should point to a JSON file that conforms to the CW721  Metadata JSON Schema.                     |
| `extension` | [TokenExtension](cw721-v1.0.0.md#tokenextension) | Any custom extension used by this contract.                                                                                                    |

### StartSale

Initiates a new crowdfund with the specified information.

{% hint style="warning" %}
Only available to the contract owner.

An Expiration must be set and not in past date.

A sale should not be already ongoing.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 StartSale {
        start_time: Option<MillisecondsExpiration>,
        end_time: MillisecondsExpiration,
        price: Coin,
        min_tokens_sold: Uint128,
        max_amount_per_wallet: Option<u32>,
        recipient: Recipient,
    },
```
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
"start_sale":{
 "start_time":10048328053820324,
 "end_time":11048328053820324,
  "price":{
    "denom":"uandr",
    "amount":"10000"
    },
  "min_tokens_sold":"1000",
  "max_amount_per_wallet":10,
  "recipient":{
   "address":"andr1..."
  }
<strong> }
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="272.3333333333333">Name</th><th width="196.50867625185924">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>start_time</code></td><td>Option&#x3C;<a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a>></td><td>An optional <a href="https://www.epochconverter.com">timestamp</a> in milliseconds to when the sale start. The sale will immediately start if not specified.</td></tr><tr><td><code>end_time</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsExpiration</a></td><td><a href="https://www.epochconverter.com">Timestamp</a> in milliseconds to specify when the sale ends.</td></tr><tr><td><code>price</code></td><td><a href="../platform-and-framework/common-types.md#coin">Coin</a></td><td>The price per token.</td></tr><tr><td><code>min_tokens_sold</code></td><td>Uint128</td><td>The minimum amount of tokens sold to go through with the sale.</td></tr><tr><td><code>max_amount_per_wallet</code></td><td>Option&#x3C;u32></td><td>The amount of tokens a wallet can purchase, default is 1.</td></tr><tr><td><code>recipient</code></td><td><a href="../platform-and-framework/common-types.md#recipient">Recipient</a></td><td>The recipient of the funds if the sale met the <code>min_tokens_sold</code>.</td></tr></tbody></table>

### Purchase

Purchases tokens based on the specified limit or amount of funds sent.

{% hint style="warning" %}
A sale needs to be in progress in order to purchase tokens.

If `number_of_tokens` is not specified, the maximum number of tokens are purchased based on the funds allocated (Unless we reach the maximum allowed).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
 Purchase {
     number_of_tokens: Option<u32>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"purchase":{
  "number_of_tokens": 10
    }
}
```
{% endtab %}
{% endtabs %}

| Name               | Type         | Description                                                                                                                                                                             |
| ------------------ | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `number_of_tokens` | Option\<u32> | An optional limit to the number of tokens to purchase. If not specified, the maximum number of tokens are purchased based on the funds allocated (Unless we reach the maximum allowed). |

### PurchaseByTokenId

Purchases a token with the specified `token_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum Execute {
 PurchaseByTokenId {
        token_id: String,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"purchase_token_by_id":{
    "token_id":"myid"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Descripton                           |
| ---------- | ------ | ------------------------------------ |
| `token_id` | String | The token Id of the NFT to purchase. |

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

Ends the sale. In the case that the minimum number of tokens to be sold is not achieved, refunds are sent to the buyers and tokens are burnt. If the minimum amount of tokens sold was achieved, the tokens are sent to the buyers and the funds to the recipient.&#x20;

{% hint style="warning" %}
The sale can only be ended if the expiration of the sale has been reached or the threshhold of min tokens sold is met.

The EndSale message needs to be called twice. Once for distribution of NFTs and another for distribution of funds.&#x20;
{% endhint %}

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
  "limit": 200
    }
 }
```
{% endtab %}
{% endtabs %}

| Name    | Type          | Description                                                                                                                                                                        |
| ------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `limit` | Option\<u32>> | An optional limit on the number of transferred tokens in case the sale was a success, or the number of refunds to issue in case the sale did not succeed (min amount not reached). |

### UpdateTokenContract

Updates the token address of the CW721 used by the Crowdfund to a new one.

{% hint style="warning" %}
Only available to the owner of the ADO.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateTokenContract { address: AndrAddr },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_token_contract":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                                                           | Description                                        |
| --------- | -------------------------------------------------------------- | -------------------------------------------------- |
| `address` | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The new token address to be used in the crowdfund. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### State

Queries the state of the sale. State is only available once a sale has started.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(State)]
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
    pub expiration: Milliseconds,
    pub price: Coin,
    pub min_tokens_sold: Uint128,
    pub max_amount_per_wallet: u32,
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
 "expiration":100384234382943243,
  "price":{
    "denom":"uandr",
    "amount":"10000"
    },
  "min_tokens_sold":"1000",
  "max_amount_per_wallet":10,
  "amount_sold":"600",
  "amount_to_send":"100000",
  "amount_transferred":"300",
  "recipient":{
    "addr":"andr1..."
  }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="267.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>expiration</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">Milliseconds</a></td><td>The expiration denoting when the sale ends as a timestamp in milliseconds.</td></tr><tr><td><code>price</code></td><td><a href="../platform-and-framework/common-types.md#coin">Coin</a></td><td>The price of each token.</td></tr><tr><td><code>min_tokens_sold</code></td><td>Uint128</td><td>The minimum number of tokens sold for the sale to go through.</td></tr><tr><td><code>max_amount_per_wallet</code></td><td>u32</td><td>The max number of tokens allowed per wallet.</td></tr><tr><td><code>amount_sold</code></td><td>Uint128</td><td>Number of tokens sold.</td></tr><tr><td><code>amount_to_send</code></td><td>Uint128</td><td>The amount of funds to send to recipient if sale successful. This already takes into account the royalties and taxes.</td></tr><tr><td><code>amount_transferred</code></td><td>Uint128</td><td>Number of tokens transferred to purchasers if sale was successful.</td></tr><tr><td><code>recipient</code></td><td><a href="../platform-and-framework/common-types.md#recipient">Recipient</a></td><td>The recipient of the raised funds if the sale is successful.</td></tr></tbody></table>

### Config

Queries the configuration of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Config)]
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
    pub token_address: String,
    pub can_mint_after_sale: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_address":"andr1...",
"can_mint_after_sale": true
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="259.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>token_address</code></td><td>String</td><td>The address of the token contract whose tokens are being sold.</td></tr><tr><td><code>can_mint_after_sale</code></td><td>bool</td><td>Whether or not the owner can mint additional tokens after the sale has been conducted.</td></tr></tbody></table>

### AvailableTokens

Queries the available tokens for sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(Vec<String>)]
     AvailableTokens {
            start_after: Option<String>,
            limit: Option<u32>,
        }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"available_tokens":{}
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                             |
| ------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `start_after` | Option\<String> | Optional parameter to specify which Token to start from. If none specified index `0` will be used. Used for pagination. |
| `limit`       | Option\<u32>    | Limit to number of available tokens to query.                                                                           |

Returns a `Vec<String>` containing the token Ids of the available NFTs for sale.

### IsTokenAvailable

Checks if the token with the specified `token_id` is available for sale.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  #[returns(bool)]
  IsTokenAvailable {
        id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_token_available":{
    "id":"token_id"
    }
 }
```
{% endtab %}
{% endtabs %}

| Name | Type   | Description                   |
| ---- | ------ | ----------------------------- |
| `id` | String | The Id of the token to check. |

Returns a `bool` response specifyi ng whether the token is available for purchase or not.

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
