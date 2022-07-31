---
description: >-
  A contract used to store data. This contract would be referenced by other
  contracts to retrieve the saved values.
---

# Primitive

## Introduction

The **Primitive ADO** is a smart contract that is used to store data for another contract. It is a simple contract that allows us to store values and give them a name to reference when needed.&#x20;

The primitive contract is used in situations where data will most likely be used several times saving us the cost of redefining it every single time. Instead, the primitive contract address can be added to the ADOs that utilize it (Found in the `InstantiationMsg` of the ADO).&#x20;

The primitive is also used to save the factory contract address for our Andromeda Apps.

**Ado\_type**: primitive

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {}
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                                                                      |
| ----------- | ------------ | -------------------------------------------------------------------------------- |
| `operators` | Vec\<String> | A vector containing the addresses to be assigned as operators over the contract. |

## ExecuteMsg

### SetValue

Sets a value for the named key. When we need to extract the value that has been saved we would use the key (name).

{% hint style="warning" %}
If `name`is not specified the default key ("default") will be used.

If `SetValue` uses a `name` that is already in use, the old `value` is overwritten by the latest `value`.

Only available to contract owner/operator.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  SetValue {
        key: Option<String>,
        value: Primitive,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "set_value": {
    "key": "rate_name",
    "value": {
      "coin": {
        "denom": "ujuno",
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
| `value` | [Primitive](primitive.md#primitive) | The value of the data.      |

#### Primitive

An enum to specify the type of data you are saving.

```rust
pub enum Primitive {
    Uint128(Uint128),
    Decimal(Decimal),
    Coin(Coin),
    String(String),
    Bool(bool),
    Vec(Vec<Primitive>),
}
```

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
        key: Option<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"delete_value":{
 "key":"rate_name"
    }
 }

```
{% endtab %}
{% endtabs %}

| Name  | Type            | Description                                                                          |
| ----- | --------------- | ------------------------------------------------------------------------------------ |
| `key` | Option\<String> | Optional key for the data to delete. If not specified, the default key will be used. |

### AndrReceive

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### AndrQuery

```rust
pub enum QueryMsg {
    AndrQuery(AndromedaQuery),
}
```

If the [`AndromedaQuery`](../../ado\_base/andrreceive-andrquery.md#andromedaquery) is of type `Get` , the contract will query the value of the specified key (data). If no data is supplied in the Get, then the contract will query the default key value.

```rust
fn handle_andromeda_query(
    deps: Deps,
    env: Env,
    msg: AndromedaQuery,
) -> Result<Binary, ContractError> {
    match msg {
        AndromedaQuery::Get(data) => match data {
            // Treat no binary as request to get value with default key.
            None => encode_binary(&query_value(deps, None)?),
            Some(_) => {
                let name: String = parse_message(&data)?;
                encode_binary(&query_value(deps, Some(name))?)
            }
        },
        _ => ADOContract::default().query(deps, env, msg, query),
    }
}
```

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery) for the rest of the default queries.
