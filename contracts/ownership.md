---
description: Ownership messages defined in each Andromeda Digital Object
---

# Ownership

Every ADO contract has a defined contract "owner", the address that currently owns the contract. Below are the message definitions for querying and updating the current ADO owner.

## ExecuteMsg

#### UpdateOwner

Updates the contract owner.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateOwner {
        address: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_owner": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description             |
| ------- | ------ | ----------------------- |
| address | String | The new contract owner. |

## QueryMsg

#### ContractOwner

Query the contract owner.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ContractOwner{
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "contract_owner": {
    }
}
```
{% endtab %}
{% endtabs %}

#### ContractOwnerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ContractOwnerResponse {
    pub owner: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "address_list": {
        "address": "terra1..."
    },
    "address_list_contract": "terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                        |
| ----- | ------ | ---------------------------------- |
| owner | String | The address of the contract owner. |
