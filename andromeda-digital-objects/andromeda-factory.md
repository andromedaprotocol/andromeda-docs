# ADO Database

## Introduction

The **Database** ADO is a smart contract that is primarily used to store code ids for ADOs to be used when creating Andromeda [Apps](../smart-contracts/andromeda-apps/app.md). The code Ids are stored within a key value pair where the key is usually the ADO name and the value is the ADO code Id.

**Ado\_type**: adodb

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {}
```
{% endtab %}
{% endtabs %}

## ExecuteMsg

### UpdateCodeId

Assigns a `code_id` to the specified `code_id_key`. This is used to store the code ids of ADOs in Apps.

{% hint style="warning" %}
Only available to the contract owner/operators.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     UpdateCodeId {
        code_id_key: String,
        code_id: u64,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_code_id": {
        "code_id_key": "adress_list",
        "code_id": 2
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type   | Description                           |
| ------------- | ------ | ------------------------------------- |
| `code_id_key` | String | The ADO key to change the code Id for |
| `code_id`     | u64    | The new code Id for the ADO.          |

### AndrReceive

Check AndrReceive.

## QueryMsg

### CodeId

Query the code\_id of the specified key.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(u64)]
    CodeId {
      key:String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "code_id": {
    "key": "..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                      |
| ----- | ------ | ------------------------------------------------ |
| `key` | String | The key of the ADO which we want the code Id of. |

Returns a u64 which represents the `code_id`.

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/andromeda-digital-objects/ado-base.md#andrquery).
