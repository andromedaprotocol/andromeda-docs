---
description: >-
  A module used to create a list of authorised addresses to interact with a
  contract
---

# Whitelist

## Definition

The `Whitelist` module uses the `AddressList` primitive to authorise addresses. When a `Whitelist` module is defined the [`AddressList`](whitelist.md#definition) module is used with the `inclusive` field set to `true`. To define a Whitelist module one of the following must be included:

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
    "whitelist": {
        "moderators": ["terra1...", "terra1..."],
        "code_id": 1
    }
}

//OR

{
    "whitelist": {
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
| address    | Option\<String>       | The contract address of the address list contract                                                          |

## Validation

The Whitelist module has the following validation requirements:

* Must be unique
* Cannot be included alongside a `Blacklist` module

## Hooks

See the [Address List Module Hooks](whitelist.md#definition).
