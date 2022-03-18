---
description: >-
  A contract used to store data. This contract would be referenced by other
  contracts to retrieve the saved values.
---

# Primitive

### Primitive

An enum to specify the type of data you are saving.

```rust
pub enum Primitive {
    Uint128(Uint128),
    Coin(Coin),
    String(String),
    Bool(bool),
    Vec(Vec<Primitive>),
}
```

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub operators: Vec<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"operators":["terra1...","terra1...","..."]
}
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                                                                      |
| ----------- | ------------ | -------------------------------------------------------------------------------- |
| `operators` | Vec\<String> | A vector containing the addresses to be assigned as operators over the contract. |

## ExecuteMsg

### SetValue

Sets a value for the named key.

{% hint style="info" %}
If `name` is not specified the default key ("default") will be used.

If `SetValue` uses a `name` that is already in use, the old `value` is overwritten by the latest `value`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  SetValue {
        name: Option<String>,
        value: Primitive,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "set_value": {
    "name": "rate_name",
    "value": {
      "coin": {
        "denom": "uusd",
        "amount": "100"
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                | Description                 |
| ------- | ----------------------------------- | --------------------------- |
| `name`  | Option\<String>                     | Optional name for the data. |
| `value` | [Primitive](primitive.md#undefined) | The value of the data.      |

### DeleteValue

Deletes the data attached to the specified `name`.

{% hint style="info" %}
If `name` is not specified the default key ("default") will be used.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
   pub enum ExecuteMsg{
   DeleteValue {
        name: Option<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"delete_value":{
 "name":"rate_name"
    }
 }

```
{% endtab %}
{% endtabs %}

| Name   | Type            | Description                           |
| ------ | --------------- | ------------------------------------- |
| `name` | Option\<String> | Optional name for the data to delete. |

