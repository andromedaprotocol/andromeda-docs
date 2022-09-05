---
description: A smart contract to implement rates on ADOs.
---

# Rates

## Introduction

The **Rates** ADO is a smart contract used to impose some kind of fees on funds transactions.

There are two types of rates which we explain in the [Rates Module](../../modules/rates-module.md) section.

This contract is implemented as a module and attached to other contracts which would apply the specified rates on any fund transfers. A small example on this would be putting rates on an NFT Collectible, sending a percentage as royalty to the original owner.

**Ado\_type**: rates

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
      "percent":{
      "percent":"0.1"
      },
      "is_additive": false,
      "recipients":[{
         "addr":"juno1..."
            },
         {
         "addr":"juno1..."
         }
         ]
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

### RateInfo

The information about the rates is stored in a `RateInfo` struct.

{% hint style="warning" %}
Each of the recipients will receive the rate imposed. ( The rate is 3% and we have 5 recipients then 15 % would go to them in total.)
{% endhint %}

```rust
pub struct RateInfo {
    pub rate: Rate,
    pub is_additive: bool,
    pub description: Option<String>,
    pub recipients: Vec<Recipient>,
}
```

| Name          | Type                                              | Description                                                                            |
| ------------- | ------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `rate`        | [Rate](rates.md#rate)                             | The type of rate being taken.                                                          |
| `is_additive` | bool                                              | An indicator to whether the rate being taken is tax. If tax `is_additive` is set to 1. |
| `description` | Option\<String>                                   | Optional description for the rate.                                                     |
| `recipients`  | Vec<[Recipient](../../common-types/recipient.md)> | The addresses to receive the `rate` specified.                                         |

#### Rate

An enum used to define various types of fees which is used in the RateInfo.

```rust
pub enum Rate {
    Flat(Coin),
    Percent(PercentRate),
    External(PrimitivePointer),
}
```

The Rate can be one of the three option seen above:

* Flat: A fixed amount to be taken ([Coin](../../common-types/coin.md)). Needs to have an amount and denomination specified.&#x20;
* Percent: A percentage based rate. Needs to have the percent to take specified.
* External: This refers to a rate that we want to use which is saved in a primitive contract. Needs the address of the primitive and the key of the stored Rate primitive to be specified.

#### PercentRate

```rust
pub struct PercentRate {
    pub percent: Decimal,
}
```

| Name      | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| `percent` | Decimal | The percentage to take as rate. |

**PrimitivePointer**

```rust
pub struct PrimitivePointer {
    pub address: AndrAddress,
    pub key: Option<String>,
}
```

| Name      | Type                                                       | Description                            |
| --------- | ---------------------------------------------------------- | -------------------------------------- |
| `address` | [AndrAddress](../../common-types/recipient.md#andraddress) | The address of the primitive contract. |
| `key`     | Option\<String>                                            | The optional key for the stored data.  |

## ExecuteMsg

### UpdateRates

{% hint style="warning" %}
Only the contract owner can execute UpdateRates.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  UpdateRates{
    rates: Vec<RateInfo>
  }
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
         "percent":{
            "percent":"0.1"
      },
         "is_additive": false,
         "recipients":[
            {
            "addr":"juno1..."
         },
            {
         "addr":"juno1..."
         },
         ...
         ]
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

### AndrReceive

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

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
         "percent":{
              "percent":"0.3"
              }
       },
      "is_additive": false,
      "recipients":[
                     {
                     "addr":"juno1..."
                  },
                     {
                     "addr":"juno1..."
                  },
         ...
         ]
      },
      ...
      ] 
  }

```
{% endtab %}
{% endtabs %}

| Name       | Type                               | Description                                               |
| ---------- | ---------------------------------- | --------------------------------------------------------- |
| `payments` | Vec<[RateInfo](rates.md#rateinfo)> | A vector of the `RatInfo` currently used by the contract. |

### AndrQuery

Check[ AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery).
