---
description: A contract to facilitate the buying and selling of ADOs.
---

# ADO Offers

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub andromeda_cw721_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"andromeda_cw721_contract"
}
```
{% endtab %}
{% endtabs %}

| Name                       | Type   | Description                                    |
| -------------------------- | ------ | ---------------------------------------------- |
| `andromeda_cw721_contract` | String | The address of the contract to give offers to. |

## ExecuteMsg

### PlaceOffer

Places an offer on the ADO with the specified `token_id.`

{% hint style="info" %}
The ADO owner is not allowed to place an offer on their ADO.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
 PlaceOffer {
        token_id: String,
        expiration: Expiration,
        offer_amount: Uint128,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"place_offer":{
  "token_id":"terra1...",
  "expiratation":{
     "at_height": 300,
     }
  "offer_amount": "200"
  }
}
  
```
{% endtab %}
{% endtabs %}

| Name           | Type       | Description                                 |
| -------------- | ---------- | ------------------------------------------- |
| `token_id`     | String     | The token id of the ADO to buy.             |
| `expiration`   | Expiration | An expiration for the offer.                |
| `offer_amount` | Uint128    | The amount of tokens to offer for the ADO.  |

### CancelOffer

Cancels an offer previously placed.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  CancelOffer {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"cancel_offer":{
  "token_id": "anewtoken"
  }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                               |
| ---------- | ------ | ----------------------------------------- |
| `token_id` | String | The id of the ADO to remove an offer on.  |

### AcceptOffer

Accepts an offer that is placed on your ADO.&#x20;

{% hint style="info" %}
Only the `andromeda_cw721_contract` can accept offers.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  AcceptOffer {
        token_id: String,
        recipient: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"accept_offer":{
 "token_id":"anewtoken",
 "recipient":"terra1..."
   }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                    |
| ----------- | ------ | ---------------------------------------------- |
| `token_id`  | String | The Id of  the token to accept the offer on.   |
| `recipient` | String | The address to receive the funds of the offer. |

## QueryMsg

### Offer

Returns the latest offer of the ADO with the given `token_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
  Offer {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"offer":{
 "token_id":"anewtoken"
  }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                     |
| ---------- | ------ | ----------------------------------------------- |
| `token_id` | String | The id of the ADO to get the latest  offer on.  |

#### OfferResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct OfferResponse {
    pub denom: String,
    pub offer_amount: Uint128,
    pub remaining_amount: Uint128,
    pub tax_amount: Uint128,
    pub expiration: Expiration,
    pub purchaser: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"denom":"uusd",
"offer_amount":"1000",
"remaining_amount":"997",
"tax_amount":"2",
"expiration":{
  "at_height": 500
    },
  "purchaser":"terra1...",
}



```
{% endtab %}
{% endtabs %}

| Name               | Type       | Description                                                                           |
| ------------------ | ---------- | ------------------------------------------------------------------------------------- |
| `denom`            | String     | The denomination of the coin being offered. Currently supports "uusd".                |
| `offer_amount`     | Uint128    | The amount of coins offered.                                                          |
| `remaining_amount` | Uint128    | The amount left after any royalties or taxes have been applied to the `offer_amount`. |
| `tax_amount`       | Uint128    | The amount of coins taken as tax.                                                     |
| `expiration`       | Expiration | Expiration for the offer.                                                             |
| `purchaser`        | String     | The address that has placed the offer                                                 |

### AllOffers

Returns all the offers of a certain purchaser.

{% tabs %}
{% tab title="Rust" %}
```rust
   pub enum ExecuteMsg{
   AllOffers {
        purchaser: String,
        limit: Option<u32>,
        start_after: Option<String>,
    }
 } 
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_offers":{
 "purchaser":"terra1...",
 "limit": 15
    }
 }
  
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                      |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------ |
| `purchaser`   | String          | The address of the purchaser we want to get the offers of.                                       |
| `limit`       | Option\<u32>    | An optional limit to the number of offers queried. The default limit is 10. The max limit is 30. |
| `start_after` | Option\<String> | An optional ID for which to start after, used for pagination.                                    |

#### AllOffersResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllOffersResponse {
    pub offers: Vec<OfferResponse>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"offers":[ 
 {
"denom":"uusd",
"offer_amount":"1000",
"remaining_amount":"997",
"tax_amount":"2",
"expiration":{
  "at_height": 500
    },
  "purchaser":"terra1...",
},
...
]
```
{% endtab %}
{% endtabs %}

| Name   | Type                                              | Description                                                                   |
| ------ | ------------------------------------------------- | ----------------------------------------------------------------------------- |
| offers | Vec<[OfferResponse](ado-offers.md#offerresponse)> | A vector of OfferResponse containing each offer with the related information. |