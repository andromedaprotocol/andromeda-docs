# Rates

## Introduction

The **Rates** ADO is a smart contract used to impose some kind of fees on funds transactions.

This contract is implemented as a module and attached to other contracts which would apply the specified rates on any fund transfers. The contract owner can specify specific addresses to be exempt from these rates. A small example on this would be putting rates on an NFT Collectible, sending a percentage as royalty to the original owner.&#x20;

There are two main types of rates:

* Taxes: Funds are added to the price and paid by the buyer
* Royalties: Funds are deducted from the price and are paid by the seller.

More information on how the module works can be found in the  [Rates Module](../../modules/rates-module.md) section.

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
         "addr":"andr1..."
            },
         {
         "addr":"andr1..."
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

Taxes are added to the selling price (Paid by buyer) while royalties are deducted from the price (Paid by seller).
{% endhint %}

```rust
pub struct RateInfo {
    pub rate: Rate,
    pub is_additive: bool,
    pub description: Option<String>,
    pub recipients: Vec<Recipient>,
}
```

| Name          | Type                                                                     | Description                                                                               |
| ------------- | ------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| `rate`        | [Rate](rates.md#rate)                                                    | The type of rate being taken.                                                             |
| `is_additive` | bool                                                                     | An indicator to whether the rate being taken is tax. If tax `is_additive` is set to true. |
| `description` | Option\<String>                                                          | Optional description for the rate.                                                        |
| `recipients`  | Vec<[Recipient](../../platform-and-framework/common-types.md#recipient)> | The addresses to receive the `rate` specified.                                            |

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

* Flat: A fixed amount to be taken ([Coin](../../platform-and-framework/common-types.md#coin)). Needs to have an amount and denomination specified.&#x20;
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

| Name      | Type                                                                    | Description                            |
| --------- | ----------------------------------------------------------------------- | -------------------------------------- |
| `address` | [AndrAddress](../../platform-and-framework/common-types.md#andraddress) | The address of the primitive contract. |
| `key`     | Option\<String>                                                         | The optional key for the stored data.  |

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
            "addr":"andr1..."
         },
            {
         "addr":"andr1..."
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

### AddExemption

Specifies an address to be exempt from any rates set by this contract.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddExemption {
         address: String 
         }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_exemption":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| `address` | String | The address that will not have the rates applied. |

### RemoveExemption

Removes an address from the list of exempt addresses.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
        RemoveExemption {
                 address: String 
                 }
        }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_exemption":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                               |
| --------- | ------ | ----------------------------------------- |
| `address` | String | The address to remove the exemption from. |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../../platform-and-framework/ado-base.md#andrrecieve) section.

### Payments

Returns the rates used by the contract in a vector of `RateInfo`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
    #[returns(PaymentsResponse)]
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

#### PaymentsResponse

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
                     "addr":"andr1..."
                  },
                     {
                     "addr":"andr1..."
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

### IsExempt

Checks if the specified address is exempt from rates.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(bool)]
    IsExempt {address: String }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_exempt":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                |
| --------- | ------ | ------------------------------------------ |
| `address` | String | The address to check if exempt from rates. |

Returns true if included and false otherwise.

### Exemptions

Queries the list of addresses exempt from rates.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum QueryMsg {   
</strong>    #[returns(ExemptionsResponse)]
    Exemptions {
        limit: Option&#x3C;u32>,
        start_after: Option&#x3C;String>,
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"exemptions":{
    "limit": 80
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                    |
| ------------- | --------------- | -------------------------------------------------------------------------------------------------------------- |
| `limit`       | Option\<u32>    | An optional limit on how many  are returned. Default limit is 50 and the maximum limit that can be set is 100. |
| `start_after` | Option\<String> | An optional Id for which to start after, used for pagination.                                                  |

#### ExemptionsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ExemptionsResponse {
    pub exemptions: Vec<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"exemptions":["andr1...","andr1...",...]
}
```
{% endtab %}
{% endtabs %}

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../../platform-and-framework/ado-base.md#andrquery).
