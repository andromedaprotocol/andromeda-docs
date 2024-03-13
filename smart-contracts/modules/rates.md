# Rates

## Introduction

The **Rates** ADO is a smart contract used to impose some kind of fees on fund transactions.

This contract is implemented as a [module](../../modules/module-definitions.md) and attached to other contracts which would apply the specified rates on any fund transfers. The contract owner can specify specific addresses to be exempt from these rates. A small example on this would be putting rates on a [CW721 ADO](../../andromeda-digital-objects/cw721.md), sending a percentage as tax/royalty to the original owner whenever an NFT is sold.&#x20;

There are two main types of rates:

* **Taxes:** Funds are added to the price and paid by the buyer.
* **Royalties**: Funds are deducted from the price and are paid by the seller.

More information on how the module works can be found in the  [Rates Module](../../modules/rates-module.md) section.

**Ado\_type**: rates

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub rates: Vec<RateInfo>,
    pub kernel_address: String,
    pub owner: Option<String>
    
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
         "address":"andr1..."
            },
         {
         "address":"andr1..."
         }
         ]
      },
    ...
   ],
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                               | Description                                                                                                                                                                                                                                                                                                                            |
| ---------------- | ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rates`          | Vec<[RateInfo](rates.md#rateinfo)> | A vector containing the different `RateInfo` of the contract.                                                                                                                                                                                                                                                                          |
| `kernel_address` | String                             | Contract address of the [kernel contract](../../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String>                    | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                            |

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
}
```

The Rate can be one of the three option seen above:

{% hint style="warning" %}
The flat rate needs to be a whole number.

The percentage rate needs to be a decimal i.e 0.2 for 20%
{% endhint %}

* **Flat**: A fixed amount to be taken ([Coin](../../platform-and-framework/common-types.md#coin)). Needs to have an amount and denomination specified.&#x20;
* **Percent**: A percentage based rate. Needs to have the percent specified.

Any other type of value will not be will not apply any rates.&#x20;

#### PercentRate

```rust
pub struct PercentRate {
    pub percent: Decimal,
}
```

| Name      | Type    | Description                     |
| --------- | ------- | ------------------------------- |
| `percent` | Decimal | The percentage to take as rate. |

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
            "address":"andr1..."
         },
            {
         "address":"andr1..."
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

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../../platform-and-framework/ado-base/) section.

## QueryMsg

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
                     "address":"andr1..."
                  },
                     {
                     "address":"andr1..."
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

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../../platform-and-framework/ado-base/) section.

## Stacking Rates

An ADO accepts the addition of multiple rates at once. This could be one of two cases:

1. Adding multiple rates using the same rates module ADO.&#x20;
2. Adding multiple rates using different rates module ADOs

#### Rates Using the Same Module

In this case, all the rates are applied on the initial price of the NFT being sold. For example, if I specify the following rates:

* 350 flat rate royalty
* 10% royalty

Then selling the NFT for 1000 uandr will take 350 uandr for the first rate and 100 uandr for the second rate.

#### Rates Using Different Modules

In this case, the rates are applied by order. After the first deductions are made, the second rate will use the remaining amount and deduct from it. For example, if I specify the following rates:

* 350 flat rate royalty using the first rates ADO&#x20;
* 10% royalty using the second rates ADO

Then selling the NFT for 1000 uandr will take 350 uandr for the first rate and 65 uandr for the second rate.
