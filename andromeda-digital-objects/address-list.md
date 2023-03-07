# Address List

## Introduction

The **Address List** ADO is a smart contract that can be used to store a list of addresses. It is one of the Andromeda [modules](../modules/module-definitions.md) that can be attached to ADOs that accept modules.&#x20;

These addresses can be set as one of the following:

* **Whitelist:** The addresses are given whitelist privilege, allowing them to  interact/execute with the ADO that has the module attached . If a whitelist of addresses is set, then any other address is considered as a blacklist.
* **Blacklist:** The addresses are given blacklist restriction, preventing them to interact/execute with the ADO that has the module attached. If a blacklist of addresses is set, then any other address is considered as a whitelist.

Blacklisted addresses are restricted from performing any execute message, but they can still query from the ADO.&#x20;

**Ado\_type:** address-list

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
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
| `is_inclusive` | bool | Whether or not the address list is inclusive. If `true` the address list is a whitelist. If false the address list is a blacklist. |

## ExecuteMsg

### AddAddress

Adds an address to the address list.

{% hint style="warning" %}
Only Owner/Operators can execute AddAddress.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AddAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "add_address": {
        "address": "andr1..."
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
pub enum ExecuteMsg {
    RemoveAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "remove_address": {
        "address": "andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| `address` | String | The address to remove from the list. |

### AndrReceive

The rest of the executes can be found in the [`AndrReceive`](../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### IncludesAddress

Query if an address is included in the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(IncludesAddressResponse)]
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
        "address": "andr1..."
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

&#x20;A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
