---
description: An ADO contract to split sent funds amongst predefined addresses.
---

# Splitter

## Splitter

The splitter's config is stored in a basic struct.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Splitter {
    pub recipients: Vec<AddressPercent>, //Map for address and percentage
    pub locked: bool,                    //Lock
    pub address_list: Option<AddressListModule>, //Address list allowing to receive funds
}
```

## AddressPercent

The splitter uses a basic array of structs to determine recipients and how the funds are divided.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct AddressPercent {
    pub addr: String,
    pub percent: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "addr": "terra1...",
    "percent": "50"
}
```
{% endtab %}
{% endtabs %}

{% hint style="warning" %}
To be a valid recipient list the array of `AddressPercent` structs must meet the following requirements:

* Be non-empty
* Have percentage amounts less than or equalling 100
{% endhint %}

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
    "recipients": ["terra1...", "terra1..."],
    "address_list": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                        | Description                                                             |
| ------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------- |
| address\_list | Option<[AddressListModule](../modules/address-list/)>       | An optional address list module for limiting authorised addresses.      |
| recipients    | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The recipient list of the splitter. Can be updated after instantiation. |

## ExecuteMsg

### UpdateRecipients

Updates the recipients of the splitter contract.

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
                "addr": "terra1...",
                "percent": "50",
            },
            ...
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                        | Description                         |
| ---------- | ----------------------------------------------------------- | ----------------------------------- |
| recipients | Vec<[AddressPercent](andromeda-splitter.md#addresspercent)> | The new list of recipient addresses |

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

| Name | Type | Description                            |
| ---- | ---- | -------------------------------------- |
| lock | bool | Whether the contract should be locked. |

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
            "moderators": ["terra1...", "terra1..."]
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                  | Description                                                        |
| ------------- | ----------------------------------------------------- | ------------------------------------------------------------------ |
| address\_list | Option<[AddressListModule](../modules/address-list/)> | An optional address list module for limiting authorised addresses. |

### **Send**

Send funds to the contract to be split amongst the recipients.&#x20;

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

### UpdateOwner

See [Ownership](ownership.md#executemsg).

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
        "recipients": ["terra1...", "terra1..."],
        "locked": true,
        "address_list": {
            "address": "terra1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type                                       | Description                                             |
| ----------------------- | ------------------------------------------ | ------------------------------------------------------- |
| config                  | [Splitter](andromeda-splitter.md#splitter) | The Splitter config struct                              |
| address\_list\_contract | Option\<String>                            | The contract address for the address list if it exists. |

### Contract Owner

See [Ownership](ownership.md#querymsg).
