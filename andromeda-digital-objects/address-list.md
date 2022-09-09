---
description: An ADO contract to store a queryable list of addresses.
---

# Address List

## Introduction

The **Address List** ADO is a smart contract that can be used to store a list of addresses.&#x20;

These addresses can be set as one of the following:

* **Whitelist:** The addresses are given whitelist privilege, allowing them to  interact with it. If a whitelist of addresses is set, then any other address is considered as a blacklist.
* **Blacklist:** The addresses are give blacklist restriction, preventing them to interact with the contract. If a blacklist of addresses is set, then any other address is considered as a whitelist.

This contract is usually implemented as a [module ](../modules/address-list-module/)to interact with other contracts setting the whitelist/blacklist for the contract.

**Ado\_type:** address-list

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub is_inclusive: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"is_inclusive": false
}
```
{% endtab %}
{% endtabs %}

| Name           | Type | Description                                                                                                                        |
| -------------- | ---- | ---------------------------------------------------------------------------------------------------------------------------------- |
| `is_inclusive` | bool | Whether or not the address list is inclusive. If `true` the address list is a whitelist, if false the address list is a blacklist. |

## ExecuteMsg

### AddAddress

Adds an address to the address list.

{% hint style="warning" %}
Only Owner/Operators can execute AddAddress.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    AddAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "add_address": {
        "address": "juno1..."
    }    
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                     |
| --------- | ------ | ------------------------------- |
| `address` | String | The address to add to the list. |

### RemoveAddress

Removes an address from the address list.

{% hint style="warning" %}
Only Owner/Operators can execute RemoveAddress.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    RemoveAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "remove_address": {
        "address": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | String | The address to remove from the list. |

### AndrReceive

Check [AndrReceive](../platform-and-framework/ado\_base.md#andrrecieve).

## QueryMsg

### IncludesAddress

Query if an address is included in the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    IncludesAddress{
        address: String
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "includes_address": {
        "address": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                               |
| --------- | ------ | ----------------------------------------- |
| `address` | String | The address for which to query inclusion. |

#### IncludesAddressResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct IncludesAddressResponse {
    pub included: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "included": false
}
```
{% endtab %}
{% endtabs %}

| Name       | Type | Description                      |
| ---------- | ---- | -------------------------------- |
| `included` | Bool | Whether the address is included. |

### AndrQuery

Check[ AndrQuery](../platform-and-framework/ado\_base.md#andrquery).