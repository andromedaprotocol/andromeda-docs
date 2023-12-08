# Weighted Distribution Splitter

## Introduction

The **Weighted-Distribution-Splitter** ADO is a smart contract to split funds among a set of defined recipients. Each of  the recipients is assigned a weight which is divided by the total weight to get the percentage of each of the recipients.&#x20;

In the Weighted Distribution Splitter ADO, each recipient is assigned a weight. The weight represents the proportion of funds that the recipient will receive. The total weight of all recipients is calculated, and then each recipient's weight is divided by the total weight to determine their percentage of the funds.

Whenever the splitter receives funds by executing a `send,` it automatically splits the funds for distribution to the defined recipients. The splitter can be locked for a predetermined period, acting as a guarantee for recipients that their allocated weights will remain constant during that time.

For example, if there are three recipients with weights 4, 7, and 2 respectively, the total weight is 13. The first recipient will receive 4/13 of the funds, the second recipient will receive 7/13 of the funds, and the third recipient will receive 2/13 of the funds.&#x20;

If a recipient is then added, the total weight is increased and the percentages are recalculated.

The contract supports [modules](broken-reference) to extend it's functionality.

**ado\_type:** weighted-distribution-splitter

## InstantiateMsg

{% hint style="warning" %}
A maximum of 100 recipients can be set.&#x20;

The minimum lock\_time that can be set is 86,400 which is 1 day.

The maximum lock\_time that can be set is 31,536,000 which is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub recipients: Vec<AddressWeight>,
    pub lock_time: Option<u64>,
    pub modules: Option<Vec<Module>>,
    pub kernel_address: String,
    pub owner: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipients":[
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 4
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 7
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 2
                  }
                ],
"kernel_address":"andr1..."
 }
```
{% endtab %}
{% endtabs %}

| Name             | Type                                                                  | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `recipients`     | Vec<[AddressWeight](weighted-distribution-splitter.md#addressweight)> | The vector of recipients for the contract. Anytime a `Send` execute message is sent the amount sent will be divided amongst these recipients depending on their assigned weight.                                                                                                                                              |
| `lock_time`      | Option\<u64>                                                          | How long the splitter is locked. When locked, no recipients/weights can be added/changed. In seconds.                                                                                                                                                                                                                         |
| `modules`        | Option\<Vec<[Module](broken-reference)>>                              | An optional vector of Andromeda[ Modules](broken-reference) that can be attached to the contract."address-list" module can be added.                                                                                                                                                                                          |
| `kernel_address` | String                                                                | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String>                                                       | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

#### AddressWeight

Struct containing the recipient of the funds and the assigned weight for that recipient.

```rust
pub struct AddressWeight {
    pub recipient: Recipient,
    pub weight: Uint128,
}
```

| Name        | Type                                                             | Description                        |
| ----------- | ---------------------------------------------------------------- | ---------------------------------- |
| `recipient` | [Recipient](../platform-and-framework/common-types.md#recipient) | The address to receive the funds.  |
| `weight`    | Uint128                                                          | The weight of the above recipient. |

## ExecuteMsg

### UpdateRecipients

Updates the recipients of the splitter. When executed, the previous recipients and distribution are replaced by the new list.

{% hint style="warning" %}
Only available to the contract owner/operator when the contract is not locked.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
      UpdateRecipients {
        recipients: Vec<AddressWeight>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_recipients":{
    "recipients":[
               {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 1
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 4
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 12
                  }
                ]
        }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                  | Description                                                        |
| ------------ | --------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `recipients` | Vec<[AddressWeight](weighted-distribution-splitter.md#addressweight)> | The new recipients of the split funds with their assigned weights. |

### AddRecipient

Add a recipient to the list.&#x20;

{% hint style="warning" %}
Only available to the contract owner/operator.

When a recipient is added, the total weight is changed and all the weights are recalculated appropriately.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   AddRecipient {
        recipient: AddressWeight,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_recipient":{
    "recipient":{
            "recipient":{
                 "address":"andr1..."
                },
            "weight": 1
              }
      }
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                             | Description                                               |
| ----------- | ---------------------------------------------------------------- | --------------------------------------------------------- |
| `recipient` | [AddressWeight](weighted-distribution-splitter.md#addressweight) | The recipient to add along with the corresponding weight. |

### RemoveRecipient

{% hint style="warning" %}
Only available to the contract owner/ operator.
{% endhint %}

Remove a recipient from the distribution.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     RemoveRecipient {
        recipient: Recipient,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_recipient":{
    "recipient":{
        "address":"andr1..."
        }
    }
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                             | Description                            |
| ----------- | ---------------------------------------------------------------- | -------------------------------------- |
| `recipient` | [Recipient](../platform-and-framework/common-types.md#recipient) | The recipient to remove from the list. |

### UpdateLock

Used to lock the contract for a certain period of time making it unmodifiable in any way. This can serve as a way to ensure for recipients that their weights from the splitter are fixed for a certain amount of time. The time is calculated in seconds.

{% hint style="warning" %}
Only available to the contract owner/operator.

Cannot be called if a lock is already in action.

The minimum time that can be set is 86,400 which is 1 day.

The maximum time that can be set is 31,536,000 which is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     UpdateLock {
        lock_time: u64,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_lock":{
    "lock_time": 200000
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type | Description                                   |
| ----------- | ---- | --------------------------------------------- |
| `lock_time` | u64  | The time in seconds to lock the contract for. |

### UpdateRecipientWeight

Updates the weight of a specific recipient from the list of recipients.

{% hint style="warning" %}
Only available to the contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg { 
     UpdateRecipientWeight {
        recipient: AddressWeight,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_recipient_weight":{
    "recipient":{
            "recipient":{
                 "address":"andr1..."
                },
            "weight": 1
              }
      }
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                             | Description                       |
| ----------- | ---------------------------------------------------------------- | --------------------------------- |
| `recipient` | [AddressWeight](weighted-distribution-splitter.md#addressweight) | The new weight for the recipient. |

### Send

Divides any attached funds to the message amongst the recipients list.

{% hint style="warning" %}
A maximum of 5 types of funds can be sent in one send message.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Send {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"send":{}
}
```
{% endtab %}
{% endtabs %}

### Base Executes

{% hint style="warning" %}
Uses the modules feature.
{% endhint %}

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base.md) section.

## QueryMsg

### GetSplitterConfig

Queries the current config of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      #[returns(GetSplitterConfigResponse)]
      GetSplitterConfig {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_splitter_config":{}
}
```
{% endtab %}
{% endtabs %}

#### GetSplitterConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct GetSplitterConfigResponse {
    pub config: Splitter,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "config":{
     "recipients":[
               {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 1
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 4
                  },
                {
                  "recipient":{
                    "address":"andr1..."
                    },
                  "weight": 12
                  }
                ],
    "lock": {
        "at_time": "1655212973934"
        }
          
    }
  }

```
{% endtab %}
{% endtabs %}

#### Splitter

```rust
pub struct Splitter {
    pub recipients: Vec<AddressWeight>,
    pub lock: Expiration,
}
```

| Name         | Type                                                                  | Description                                                                                                                                                                                                                   |
| ------------ | --------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `recipients` | Vec<[AddressWeight](weighted-distribution-splitter.md#addressweight)> | The vector of recipients for the contract. Anytime a `Send` execute message is sent the amount sent will be divided amongst these recipients depending on their assigned weight.                                              |
| `locked`     | [Expiration](../platform-and-framework/common-types.md#expiration)    | The expiration time of the lock. Will return an epoc time which is equal to the current\_time _+_ lock\_time taken at the point of setting the lock. (Current time refers to the time the lock was set and not the time now.) |

### GetUserWeight

Queries the user's allocated weight.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetUserWeightResponse)]
    GetUserWeight {
        user: Recipient,
        }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_user_weight":{
    "user":{
        "address":"andr1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type      | Description                               |
| ------ | --------- | ----------------------------------------- |
| `user` | Recipient | The user we want to check the weight for. |

#### GetUserWeightResponse

```rust
pub struct GetUserWeightResponse {
    pub weight: Uint128,
    pub total_weight: Uint128,
}
```

| Name           | Type    | Description                       |
| -------------- | ------- | --------------------------------- |
| `weight`       | Uint128 | The weight of the user.           |
| `total_weight` | Uint128 | The total weight of the splitter. |

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base.md) section.
