---
description: A module used to create a list of unauthorised addresses
---

# Blacklist

## Definition

The `Blacklist` module uses the `AddressList` primitive to authorise addresses. When a `Blacklist` module is defined the [`AddressList`](blacklist.md#definition) module is used with the `inclusive` field set to `false`. To define a Blacklist module one of the following must be included:

* A list of moderators and a valid contract code id
* A valid `AddressList` contract address

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Whitelist {
    pub moderators: Option<Vec<String>>,
    pub code_id: Option<u64>,
    pub address: Option<String>
}
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
{
    "blacklist": {
        "moderators": ["terra1...", "terra1..."],
        "code_id": 1
    }
}

//OR

{
    "blacklist": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}



| Name       | Type                  | Description                                                                                                |
| ---------- | --------------------- | ---------------------------------------------------------------------------------------------------------- |
| moderators | Option\<Vec\<String>> | The list of addresses authorised to moderate the address list. Must be included with the `code_id` field.  |
| code_id    | Option\<u64>          | The code id to use to instantiate the address list contract. Must be included with the `moderators` field. |
| address    | Option\<String>       | A valid `AddressList` contract address. Highest priority.                                                  |

## Validation

The Blacklist module has the following validation requirements:

* Must be unique
* Cannot be included alongside a `Whitelist` module

## Hooks

See the [Address List Module Hooks](blacklist.md#definition).

