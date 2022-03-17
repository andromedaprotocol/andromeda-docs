---
description: An ADO contract to split sent funds amongst predefined addresses.
---

# Splitter

## Splitter

The splitter's config is stored in a basic struct.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, 
    pub locked: bool,                   
    pub address_list: Option<AddressListModule>, 
}
```

| Name           | Type                                                       | Description                                                                                                                                                                          |
| -------------- | ---------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `recipients`   | Vec<[AdressPercent](andromeda-splitter.md#addresspercent)> | The vector of recipients for the contract. Anytime a `Send` execute message is sent the amount sent will be divided amongst these recipients depending on their assigned percentage. |
| `locked`       | bool                                                       | Whether or not the contract is currently locked. This restricts updating any config related fields.                                                                                  |
| `address_list` | Option<[AddressListModule](../modules/address-list/)>      | An optional address list to restrict access to the `Splitter` contract.                                                                                                              |

## AddressPercent

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
    "recipient": "terra1...",
    "percent": "50"
}

```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
To be a valid recipient list the array of `AddressPercent` structs must meet the following requirements:

* Be non-empty
* Have percentage amounts less than or equaling 100
{% endhint %}

#### Recipient

An enum containing the types of recipients.

```rust
pub enum Recipient {
    Addr(String),
    ADO(ADORecipient),
}
```

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub recipients: Vec<AddressPercent>,
    pub address_list: Option<AddressListModule>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "recipients": [
               {
    "recipient":"terra1...", 
     "percent":20
     },
     ...
    ],
    
  "address_list": {
            "code_id": 1,
            "operators": ["terra1...", "terra1..."]
            "inclusive":true,
        }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                        | Description                                                             |
| -------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------- |
| `address_list` | Option<[AddressListModule](../modules/address-list/)>       | An optional address list module for limiting authorized addresses.      |
| `recipients`   | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The recipient list of the splitter. Can be updated after instantiation. |

{% hint style="info" %}
Anytime a [`Send`](andromeda-splitter.md#send) execute message is sent the amount sent will be divided amongst the recipients depending on their assigned percentage.
{% endhint %}

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
                "recipient": "terra1...",
                "percent": "50",
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

### UpdateAddressList

Updates the `address_list` config field.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
   UpdateAddressList { 
      address_list: Option<AddressListModule>
   },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "updated_address_list": {
        "address_list": {
            "code_id": 1,
            "operators": ["terra1...", "terra1..."]
            "inclusive":true,
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                                                  | Description                                                        |
| -------------- | ----------------------------------------------------- | ------------------------------------------------------------------ |
| `address_list` | Option<[AddressListModule](../modules/address-list/)> | An optional address list module for limiting authorized addresses. |

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
    "send": {
    }
}
```
{% endtab %}
{% endtabs %}

## QueryMsg

### GetSplitterConfig

Query the address of a given symbol.

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
    "get_splitter_config": {
    }
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
    pub address_list_contract: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "config": {
        "recipients": [
            {
                "recipient": "terra1...",
                "percent": "50",
            },
            ...
        ],
        "locked": true,
        "address_list_contract": {
            "address": "terra1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                       | Description                                             |
| ----------------------- | ------------------------------------------ | ------------------------------------------------------- |
| `config`                | [Splitter](andromeda-splitter.md#splitter) | The Splitter config struct.                             |
| `address_list_contract` | Option\<String>                            | The contract address for the address list if it exists. |
