---
description: An ADO contract to store a queryable list of addresses.
---

# Address List

## Introduction

The **Address List** ADO is a smart contract that can be used to store a list of addresses.&#x20;

These addresses can be set as one of the following:

\
**Whitelist:** The addresses are given whitelist privilege, allowing them to  interact with it. If a whitelist of addresses is set, then any other address is considered as a blacklist.&#x20;

**Blacklist:** The addresses are give blacklist restriction, preventing them to interact with the contract. If a blacklist of addresses is set, then any other address is considered as a whitelist.

This contract is usually implemented as a module to interact with other contracts setting the whitelist/blacklist for the contract.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub operators: Vec<String>,
    pub is_inclusive: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "operators": ["terra1...","terra...",...],
}
```
{% endtab %}
{% endtabs %}

| Name           | Type         | Description                                                                                                                        |
| -------------- | ------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| `operators`    | Vec\<String> | A list of addresses that are authorized or unauthorized to interact with the contract.                                             |
| `is_inclusive` | bool         | Whether or not the address list is inclusive. If `true` the address list is a whitelist, if false the address list is a blacklist. |

## ExecuteMsg

### AddAddress

Adds an address to the address list.

{% hint style="info" %}
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
        "address": "terra1..."
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

{% hint style="info" %}
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
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | String | The address to remove from the list. |

### UpdateOwner/UpdateOperators

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md).

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
        "address": "terra1..."
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

### Owner/Operators/IsOperator

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md).
