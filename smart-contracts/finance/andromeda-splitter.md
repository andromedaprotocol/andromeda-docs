---
description: An ADO contract to split sent funds amongst predefined addresses.
---

# Splitter

## Introduction

The **Splitter** ADO is a smart contract used to split funds to a preset number of addresses. Each of the addresses has a specific percentage assigned by the contract owner.&#x20;

**Ado\_type**: splitter

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub recipients: Vec<AddressPercent>,
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
                    "addr":"juno1...", 
                },
                "percent":"0.2"
     },
     ...
    ],
    
  "modules": [
        {
          "module_type": "address_list",
          "address": {
             "identifier": "my_address_list",
          },
          "is_mutable": true
        },
        ...
    ]
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                        | Description                                                                    |
| ------------ | ----------------------------------------------------------- | ------------------------------------------------------------------------------ |
| `Modules`    | Option<[Module](../../modules/module-definitions.md)>       | An optional vector of Andromeda Modules.  "address\_list" module can be added. |
| `recipients` | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The recipient list of the splitter. Can be updated after instantiation.        |

{% hint style="warning" %}
Anytime a [`Send`](andromeda-splitter.md#send) execute message is sent the amount sent will be divided amongst the recipients depending on their assigned percentage.
{% endhint %}

#### AddressPercent

The splitter uses a basic array of structs to determine recipients and how the funds are divided.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
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

Read more about the recipient struct [here](../../common-types/recipient.md).

## ExecuteMsg

### UpdateRecipients

Updates the recipients of the splitter contract. Only executable by the contract owner when the contract is not locked.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
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
                }
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

Sets a lock on the contract, disallowing editing of any other config fields.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateLock {
        lock: bool,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_lock": {
        "lock": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type | Description                            |
| ------ | ---- | -------------------------------------- |
| `lock` | bool | Whether the contract should be locked. |

### **Send**

Divides any attached funds to the message amongst the recipients list.

{% hint style="info" %}
You cannot send more than 5 coins with one Send.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
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

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### GetSplitterConfig

The current config of the Splitter contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetSplitterConfig{},
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
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
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
        "locked": true
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
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, 
    pub locked: bool,                   
}
```

| Name         | Type                                                       | Description                                                                                                                                                                          |
| ------------ | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `recipients` | Vec<[AdressPercent](andromeda-splitter.md#addresspercent)> | The vector of recipients for the contract. Anytime a `Send` execute message is sent the amount sent will be divided amongst these recipients depending on their assigned percentage. |
| `locked`     | bool                                                       | Whether or not the contract is currently locked. This restricts updating any config related fields.                                                                                  |

### AndrQuery

Check[ AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery).
