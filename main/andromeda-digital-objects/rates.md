# Rates

The Rates ADO is a smart contract that can be used to hold rates configurations for other ADOs. Instead of setting up the rate on the ADO itself, the ADO can reference this ADO to extract the rates set up and apply them on the ADO. These rates can be used to add a fee that is payed on a messages whether a tax or royalty.&#x20;

We have seen in the [AndromedaMsg](../platform-and-framework/ado-base/andromedamsg.md) section that rates can be applied on ADOs that have the rates feature enabled. This ADO can be referenced by those ADOs to apply the rates specified here.

**ado\_type:** rates

**Version: 2.0.4**

## InstantiateMsg

{% hint style="warning" %}
By actions, we are referring to execute messages.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub action: String,
    pub rate: LocalRate,
    pub kernel_address: String,
    pub owner: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"action":"Buy",
"rate":{
    "rate_type":"additive",
    "recipients":["andr1...","andr2..."],
    "value":{
          "percent":{
                "percent": "0.05"
                }
            },
     "description":"a 5% tax on minting"
     },
 "kernel_address":"andr1..."
 }
```
{% endtab %}
{% endtabs %}

| Name             | Type                            | Description                                                                                                                           |
| ---------------- | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `action`         | String                          | The message to apply the rates on.                                                                                                    |
| `rate`           | [LocalRate](rates.md#localrate) | The rate configurations.                                                                                                              |
| `kernel_address` | String                          | Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts. |
| `owner`          | Option\<String>                 | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                           |

### ExecuteMsg

### SetRate

{% hint style="warning" %}
Only available to the ADO owner
{% endhint %}

Sets a rate on the specified action.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    SetRate { action: String, rate: LocalRate },
    }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"action":"Buy",
"rate":{
    "rate_type":"additive",
    "recipients":["andr1...","andr2..."],
    "value":{
          "percent":{
                "percent": "0.05"
                }
            },
     "description":"a 5% tax on minting"
     }
 }
```
{% endtab %}
{% endtabs %}

| Name     | Type                            | Description                          |
| -------- | ------------------------------- | ------------------------------------ |
| `action` | String                          | The execute message to add rates on. |
| `rate`   | [LocalRate](rates.md#localrate) | The rate configurations.             |

#### LocalRate

```rust
pub struct LocalRate {
    pub rate_type: LocalRateType,
    pub recipients: Vec<Recipient>,
    pub value: LocalRateValue,
    pub description: Option<String>,
}
```

| Name          | Type                                                                                                        | Description                                                                                                                                                                                    |
| ------------- | ----------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `rate_type`   | [LocalRateType](rates.md#localratetype)                                                                     | The type of rate.                                                                                                                                                                              |
| `recipients`  | Vec<[Recipient](https://docs.andromedaprotocol.io/andromeda/platform-and-framework/common-types#recipient)> | The addresses to recieve the rates. Each address specified will receive the full amount of rate. For example, having a rate of 5% and 2 recipients means each address will receive a 5% rate.  |
| `value`       | [LocalRateValue](rates.md#localratevalue)                                                                   | The amount of funds taken.                                                                                                                                                                     |
| `description` | Option\<String>                                                                                             | Optional description for the purpose of the rate.                                                                                                                                              |

#### LocalRateType

An enum specifying the type of the rate.

```rust
pub enum LocalRateType {
    Additive,
    Deductive,
}
```

* **Additive:** The rate amount acts as a tax and is added to the price and payed by the buyer. For example a 10% additive rate (with one recipient) on a price of 1000 uandr means the buyer has to send 1100 uandr.
* **Deductive:** The rate amount acts as a royalty and is deducted from the price. For example a 10% deductive rate on a price of 1000 uandr means the buyer needs to send 1000 uand and 100 will be taken to the recipient of the rate.

#### LocalRateValue

{% hint style="warning" %}
Percentage is specified as a decimal. This means a rate of 10% is specified as 0.1 and not 10.
{% endhint %}

```rust
pub enum LocalRateValue {
    Percent(PercentRate),
    Flat(Coin),
} 

pub struct PercentRate {
    pub percent: Decimal,
}
```

* **Percent:** The rate is specified as a percentage of the price using decimal.
* **Flat:** The rate is specified as a fixed amount of [Coin](https://docs.andromedaprotocol.io/andromeda/platform-and-framework/common-types#coin).

### RemoveRate

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

Removes the rate set on the specified action.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum RatesMessage {
  RemoveRate {
    action: String 
  },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"rates":{
    "remove_rate":{
        "action":"Mint"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                    |
| -------- | ------ | ---------------------------------------------- |
| `action` | String | The execute message to remove the rates from.  |

### Base Executes

The rest of the execute messages can be found in the [ADO Base](../platform-and-framework/ado-base/andromedamsg.md) section.

## QueryMsg

### Rate

Queries the rates applied for the specified action.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(RateResponse)]
    Rate { action: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"rate":{
    "action":"Buy"
    }
}
```
{% endtab %}
{% endtabs %}

Returns a [LocalRate](rates.md#localrate) struct containing the configurations of the rate.

### Base Queries

The rest of the query messages can be found in the [ADO Base](../platform-and-framework/ado-base/andromedaquery.md) section.
