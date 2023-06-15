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

<table><thead><tr><th width="261.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>code_id_key</code></td><td>String</td><td>The ADO key to change the code Id for</td></tr><tr><td><code>code_id</code></td><td>u64</td><td>The new code Id for the ADO.</td></tr></tbody></table>

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
    "key": "cw721"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                      |
| ----- | ------ | ------------------------------------------------ |
| `key` | String | The key of the ADO which we want the code Id of. |

Returns a u64 which represents the `code_id`.

### AdoTypes

Lists all the available ADO types.

{% tabs %}
{% tab title="Rust" %}
```rust
#[returns(Vec<String>)]
     AdoTypes {
         limit: Option<u32>,
         start_after: Option<String>,
     }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_types":{
    "limit": 30,
    "start_after": "rates"
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type             | Description                                                                                                  |
| ------------- | ---------------- | ------------------------------------------------------------------------------------------------------------ |
| `limit`       | Option\<u32>     | An optional limit to the number of ADO types returned. The default limit is 25 and the maximum limit is 100. |
| `start_after` | Option\<String>  | Optional Id to start after.                                                                                  |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../platform-and-framework/ado-base.md#andrquery).
