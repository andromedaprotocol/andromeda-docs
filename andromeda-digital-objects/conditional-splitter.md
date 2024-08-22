# Conditional Splitter

## Introduction

The Conditional Splitter ADO is a smart contract used to split funds to specified recipients based on the amount that is sent. At instantiation, the owner specifies the recipient sets along with the minimum amount of funds needed for each set.&#x20;

The ADO can be locked by the owner meaning that the recipient lists cannot be changed for the duration of the lock.

**Example:**

A user has 3 recurring monthly payments that they need to pay:

1. **Employee Salaries:** 5000 tokens needed to pay the salaries of 5 employees.
2. **Family Allowances:** User needs 1000 tokens to be distributed to 4 family members as a monthly allowance.
3. **Subscription Payments:** 300 tokens needed to pay for 2 subscription services.

Instead of having to do this manually every month, the user can set up the conditional splitter as follows:

1. First list of recipients are the employee addresses wiht a min threshold of 5000
2. Seconds set of recipients are the family addresses with a threshold of 500
3. Third set of recipients are the addresses for the subscription services with a threshold of 300

{% hint style="info" %}
For each split, the owner specifies the percentage of each recipient.
{% endhint %}

This means that if the amount of funds sent is **between** 300 and 1000, they will go to the subscription addresses. If the amount sent is **between** 1000 and 5000, then they are split between the family addresses. Finally, if the amount sent is greater or equal to 5000, then the funds are split between the employee addresses.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub thresholds: Vec<Threshold>,
    pub lock_time: Option<MillisecondsDuration>,
    pub kernel_address:String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "thresholds": [
    {
      "min": "1000",
      "address_percent": [
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.5"
        },
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.5"
        }
      ]
    },
    {
      "min": "5000",
      "address_percent": [
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.7"
        },
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.3"
        }
      ]
    }
  ],
  "lock_time": "6000000",
  "kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="249.33333333333331">Name</th><th width="252.39014373716634">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>thresholds</code></td><td>Vec&#x3C;<a href="conditional-splitter.md#threshold">Threshold</a>></td><td>The sets of recipients, their weights and the min threshold amount.</td></tr><tr><td><code>lock_time</code></td><td>Option&#x3C;<a href="broken-reference">MillisecondsDuration</a>></td><td>How long the splitter is locked. When locked, no recipients can be added/changed. Duration specified in milliseconds.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="broken-reference">kernel contract</a> to be used for <a href="broken-reference">AMP</a> messaging. Kernel contract address can be found in our <a href="broken-reference">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

#### Threshold

```rust
pub struct Threshold {
    pub min: Uint128,
    pub address_percent: Vec<AddressPercent>,
}
```

| Name              | Type                                                          | Description                                                                                           |
| ----------------- | ------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `min`             | Uint128                                                       | The minimum amount of funds needed to be sent for the corresponding `address_percent` to be applied.  |
| `address_percent` | Vec<[AddressPercent](conditional-splitter.md#addresspercent)> | The addresses and percentages for the recipients.                                                     |

#### AddressPercent

```rust
pub struct AddressPercent {
    pub recipient: Recipient,
    pub percent: Decimal,
}
```

| Name        | Type                          | Description                                            |
| ----------- | ----------------------------- | ------------------------------------------------------ |
| `recipient` | [Recipient](broken-reference) | The address to receive the specified percent.          |
| `percent`   | Decimal                       | The percent to be received by the specified recipient. |

## ExecuteMsg

### UpdateThresholds

Update the list of recipients and thresholds.

{% hint style="warning" %}
Only available to the ADO owner.

Cannot be called if there is an existing lock that has not expired yet.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateThresholds {
       thresholds: Vec<Threshold> 
       },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "thresholds": [
    {
      "min": "7000",
      "address_percent": [
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.3"
        },
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.7"
        }
      ]
    },
    {
      "min": "5000",
      "address_percent": [
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.2"
        },
        {
          "recipient": {
            "address": "andr1..."
          },
          "percent": "0.8"
        }
      ]
    }
  ]
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                | Description                                    |
| ------------ | --------------------------------------------------- | ---------------------------------------------- |
| `thresholds` | Vec<[Threshold](conditional-splitter.md#threshold)> | The new recipient sets and minimum thresholds. |

### UpdateLock

Add a new lock time for the recipient sets.

{% hint style="warning" %}
Only available to the ADO owner.

Cannot be called if there is an existing lock that has not expired yet.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     UpdateLock {
        lock_time: MillisecondsDuration,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}

{% endtab %}
{% endtabs %}

| Name        | Type                                     | Description                                                                                                           |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `lock_time` | [MillisecondsDuration](broken-reference) | How long the splitter is locked. When locked, no recipients can be added/changed. Duration specified in milliseconds. |

### Send

Divides any attached funds to the message amongst the recipients.

{% hint style="warning" %}
The funds are sent to the recipient set with the closest threshold that is less than the amount sent. For example if we have thresholds 300, 600, 1500, and 2000, then sending 400 goes to the first set, 1400 to the second set, 1980 to the third set, and anything equal or greater than 2000 to the fourth set.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Send {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"send"{}
}
```
{% endtab %}
{% endtabs %}

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](broken-reference) section.

## QueryMsg

### GetConditionalSplitterConfig

Queriest the splitter's recipient sets and thresholds and lock time if found.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetConditionalSplitterConfigResponse)]
    GetConditionalSplitterConfig {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_conditional_splitter_config"{}
}
```
{% endtab %}
{% endtabs %}

#### GetConditionalSplitterConfigResponse

```rust
pub struct GetConditionalSplitterConfigResponse {
    pub config: ConditionalSplitter,
}

pub struct ConditionalSplitter {
    pub thresholds: Vec<Threshold>,
    pub lock_time: Option<MillisecondsExpiration>,
}
```

### Base Queries

The rest of the query messages can be found in the[ ADO Base](broken-reference) section.
