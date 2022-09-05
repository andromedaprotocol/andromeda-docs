# Factory

## Introduction

The **Factory** ADO is a smart contract that is primarily used to store code ids for contracts when creating Andromeda [Apps](app.md).

**Ado\_type**: factory

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {}
```
{% endtab %}
{% endtabs %}

## ExecuteMsg

### UpdateCodeId

Assigns a `code_id` to the specified `code_id_key`. This is used to store the code ids of ADOs in Apps.

{% hint style="info" %}
Only available to the contract owner/operators.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
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

| Name              | Type         | Description                                                                       |
| ----------------- | ------------ | --------------------------------------------------------------------------------- |
| `receipt_code_id` | Option\<u64> | Optional new code ID for the [receipt](../modules/receipt-contract.md) contracts. |
| `token_code_id`   | Option\<u64> | Optional new code ID for the contract.                                            |

### AndrReceive

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### CodeId

Query the code\_id of the specified key.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    CodeId {
      key:String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "code_ids": {
    "key": "..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                           |
| ----- | ------ | ----------------------------------------------------- |
| `key` | String | The key of the contract which we want the code id of. |

Returns a u64 which represents the `code_id`.

### AndrQuery

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md).
