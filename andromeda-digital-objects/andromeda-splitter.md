---
description: An ADO contract to split sent funds amongst predefined addresses.
---

# Splitter

## Introduction

The **Splitter** ADO is a smart contract used to split funds to a preset number of addresses. Each of the addresses has a specific percentage assigned by the contract owner. The splitter can be locked for a specified time as a kind of insurance for recipients that their weights will not be changed for a certain period of time.

The contract supports [modules](broken-reference) to extend it's functionality.

**Ado\_type**: splitter

## InstantiateMsg

{% hint style="warning" %}
A maximum of 100 recipients can be set.&#x20;

The minimum time that can be set is 86,400 which is 1 day.

The maximum time that can be set is 31,536,000 which is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub recipients: Vec<AddressPercent>,
    pub lock_time: Option<u64>
    pub modules: Option<Module>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "recipients": [
               {
                "recipient":{
                    "addr":"juno1..."
                },
                "percent":"0.2"
     },
     ...
    ],
    
  "modules": [
        {
          "module_type": "address_list",
          "address": {
             "identifier": "my_address_list"
          },
          "is_mutable": true
        },
        ...
    ]
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                        | Description                                                                                                                           |
| ------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `Modules`    | Option<[Module](../modules/module-definitions.md)>          | An optional vector of Andromeda[ Modules](broken-reference) that can be attached to the contract. "address-list" module can be added. |
| `lock_time`  | Option\<u64>                                                | How long the splitter is locked. When locked, no recipients can be added/changed.                                                     |
| `recipients` | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The recipient list of the splitter. Can be updated after instantiation.                                                               |

{% hint style="warning" %}
Anytime a [`Send`](andromeda-splitter.md#send) execute message is sent, the amount sent will be divided amongst the recipients depending on their assigned percentage.
{% endhint %}

#### AddressPercent

The splitter uses a basic array of structs to determine recipients and how the funds are divided.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AddressPercent {
    pub recipient: Recipient,
    pub percent: Decimal,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "recipient":{
        "addr":"juno1..."
     },
    "percent": "0.5"
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
To be a valid recipient list the array of `AddressPercent` structs must meet the following requirements:

* Be non-empty
* Have percentage amounts less than or equaling 1
{% endhint %}

Read more about the recipient struct [here](broken-reference).

## ExecuteMsg

### UpdateRecipients

Updates the recipients of the splitter contract. Only executable by the contract owner when the contract is not locked.

{% hint style="warning" %}
Only available to the contract owner/operator when the contract is not locked.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateRecipients { 
        recipients: Vec<AddressPercent> 
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_recipients": {
        "recipients": [
            {
                "recipient":{
                    "addr":"juno1..."
                },
                "percent": "0.5"
            },
            ...
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                        | Description                                 |
| ------------ | ----------------------------------------------------------- | ------------------------------------------- |
| `recipients` | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The new list of addresses to receive funds. |

### UpdateLock

Used to lock the contract for a certain period of time making it unmodifiable in any way. This can serve as a way to ensure for recipients that their weights from the splitter are fixed for a certain amount of time. The time is calculated in seconds.

{% hint style="warning" %}
Only available to the contract owner/operator when the contract is not already locked.

The minimum time that can be set is 86,400 which is 1 day.

The maximum time that can be set is 31,536,000 which is 1 year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateLock {
        lock_time: u64,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_lock": {
        "lock_time": 200000
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type | Description                                                                       |
| ----------- | ---- | --------------------------------------------------------------------------------- |
| `lock_time` | u64  | How long the splitter is locked. When locked, no recipients can be added/changed. |

### **Send**

Divides any attached funds to the message amongst the recipients list.

{% hint style="info" %}
You cannot send more than 5 coins with one Send.
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
```javascript
{
"send": {}
}
```
{% endtab %}
{% endtabs %}

### AndrReceive

{% hint style="info" %}
Uses the modules feature.
{% endhint %}

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### GetSplitterConfig

The current config of the Splitter contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetSplitterConfig {},
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"get_splitter_config": {}
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
```javascript
{
    "config": {
        "recipients": [
            {
                "recipient":
                { 
                    "juno1..."
                    },
                "percent": "0.5"
            },
            ...
        ],
        "locked": {
            "at_time": "1655212973"
        }
            
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                       | Description                 |
| -------- | ------------------------------------------ | --------------------------- |
| `config` | [Splitter](andromeda-splitter.md#splitter) | The Splitter config struct. |

#### Splitter

The splitter's config is stored in a basic struct.

```rust
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, 
    pub lock: Expiration,                   
}
```

| Name         | Type                                                       | Description                                                                                                                                                                                                                   |
| ------------ | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `recipients` | Vec<[AdressPercent](andromeda-splitter.md#addresspercent)> | The vector of recipients for the contract. Anytime a `Send` execute message is sent the amount sent will be divided amongst these recipients depending on their assigned percentage.                                          |
| `locked`     | Expiration                                                 | The expiration time of the lock. Will return an epoc time which is equal to the current\_time _+_ lock\_time taken at the point of setting the lock. (Current time refers to the time the lock was set and not the time now.) |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
