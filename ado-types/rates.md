---
description: A smart contract to implement rates on ADOs.
---

# Rates

### RateInfo

The information about the rates is stored in a `RateInfo` struct.

{% hint style="warning" %}
Each of the recipients will receive the rate imposed on the auction. ( The rate is 3% and we have 5 recipients then 15 % would go to them in total.)
{% endhint %}

```rust
pub struct RateInfo {
    pub rate: Rate,
    pub is_additive: bool,
    pub description: Option<String>,
    pub receivers: Vec<Recipient>,
}
```

| Name          | Type                       | Description                                                                            |
| ------------- | -------------------------- | -------------------------------------------------------------------------------------- |
| `rate`        | [Rate](rates.md#undefined) | The type of rate being taken.                                                          |
| `is_additive` | bool                       | An indicator to whether the rate being taken is tax. If tax `is_additive` is set to 1. |
| `description` | Option\<String>            | Optional description for the rate.                                                     |
| `receivers`   | Vec\<Recipient>            | The addresses to receive the `rate` specified.                                         |

### Rate

An enum used to define various types of fees which is used in the RateInfo.

```rust
pub enum Rate {
    Flat(Coin),
    Percent(Uint128),
    External(ADORate),
}
```

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub rates: Vec<RateInfo>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "rates":
[
   { 
      "rate":{
      "percent":"3"
      },
      "is_additive": false,
      "receivers":["terra1...","terra2...",...]
      },
      ...
      ] 
   }
```
{% endtab %}
{% endtabs %}

| Name    | Type                               | Description                                                   |
| ------- | ---------------------------------- | ------------------------------------------------------------- |
| `rates` | Vec<[RateInfo](rates.md#rateinfo)> | A vector containing the different `RateInfo` of the contract. |

## ExecuteMsg

### UpdateRates

{% hint style="info" %}
Only the contract owner can execute UpdateRates.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg{
  UpdateRates {
   rates: Vec<RateInfo>
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_rates":{
  "rates":
  [
     { 
      "rate":{
      "percent":"3"
      },
      "is_additive": false,
      "receivers":["terra1...","terra2...",...]
      },
      ...
      ] 
    }
  }
  

```
{% endtab %}
{% endtabs %}

| Name    | Type                               | Description                                                        |
| ------- | ---------------------------------- | ------------------------------------------------------------------ |
| `rates` | Vec<[RateInfo](rates.md#rateinfo)> | A vector containing the new `RateInfo` to be used by the contract. |

## QueryMsg

### Payments

Returns the rates used by the contract in a vector of `RateInfo`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
Payments {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"payments":{}
}
```
{% endtab %}
{% endtabs %}

### PaymentsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PaymentsResponse {
    pub payments: Vec<RateInfo>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"payments":
 [
     { 
      "rate":{
      "percent":"3"
      },
      "is_additive": false,
      "receivers":["terra1...","terra2...",...]
      },
      ...
      ] 
    }
  }

```
{% endtab %}
{% endtabs %}

| Name       | Type                               | Description                                               |
| ---------- | ---------------------------------- | --------------------------------------------------------- |
| `payments` | Vec<[RateInfo](rates.md#rateinfo)> | A vector of the `RatInfo` currently used by the contract. |

