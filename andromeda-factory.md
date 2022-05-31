---
description: A factory contract for generating Andromeda Digital Objects
---

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

### Create

Creates a new [ADO](ado-classes/andromeda-digital-object.md) contract. The sender of the message is defined as the minter for the ADO contract and as such, the owner of the ADO contract.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    Create {
        name: String,
        symbol: String
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "create": {
        "name": "Example Token",
        "symbol": "ET",
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description      |
| -------- | ------ | ---------------- |
| `name`   | String | The ADO's name   |
| `symbol` | String | The ADO's symbol |

### UpdateAddress

Updates the ADO contract address for the given `symbol`.&#x20;

{% hint style="info" %}
Only available to the ADO owner or contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateAddress {
        symbol: String,
        new_address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_address": {
        "symbol": "ET",
        "new_address" : "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type   | Description                                 |
| ------------- | ------ | ------------------------------------------- |
| `symbol`      | String | The ADO's symbol to change the address for. |
| `new_address` | String | The new contract address.                   |

### UpdateCodeId

Updates the Code Id of the contract.

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

| Name              | Type         | Description                                                                                   |
| ----------------- | ------------ | --------------------------------------------------------------------------------------------- |
| `receipt_code_id` | Option\<u64> | Optional new code ID for the [receipt](modules/receipt-module/receipt-contract.md) contracts. |
| `token_code_id`   | Option\<u64> | Optional new code ID for the [ADO](ado-classes/andromeda-digital-object.md) contracts.        |

### AndrReceive

Check [AndrReceive](ado\_base/andrreceive-andrquery.md).

## QueryMsg

### GetAddress

Query the contract address of a given symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetAddress {
        symbol: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_address": {
        "symbol": "ET"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                    |
| -------- | ------ | ---------------------------------------------- |
| `symbol` | String | The ADO symbol for which to query the address. |

#### AddressResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct AddressResponse {
    pub address: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
 "address": "juno1..."
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                   |
| --------- | ------ | --------------------------------------------- |
| `address` | String | The contract address of the given ADO symbol. |

### CodeId

Query the address of a given symbol.

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

Check [AndrQuery](ado\_base/andrreceive-andrquery.md).
