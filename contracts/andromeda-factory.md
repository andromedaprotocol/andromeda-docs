---
description: A factory contract for generating Andromeda Digital Objects
---

# Andromeda Factory

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub token_code_id: u64,
    pub receipt_code_id: u64,
    pub address_list_code_id: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "token_code_id": 1,
    "receipt_code_id": 2,
    "address_list_code_id": 3
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type | Description                                                            |
| -------------------- | ---- | ---------------------------------------------------------------------- |
| token_code_id        | u64  | The code ID for the token contract                                     |
| receipt_code_id      | u64  | The code ID for the [receipt](andromeda-receipts.md) contract          |
| address_list_code_id | u64  | The code ID for the [address list](andromeda-address-list.md) contract |

## ExecuteMsg

### Create

Creates a new token contract using the defined  `token_code_id`. The address of the instantiated contract is then registered under the token's symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    Create {
        name: String,
        symbol: String,
        modules: Vec<ModuleDefinition>,
        metadata_limit: Option<u64>
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
        "extensions": [
            {
                "whitelist": {
                    "moderators": ["terra1..."]
                }
            },
            {
                "taxable": {
                    "tax": 4,
                    "receivers": ["terra1...", "terra1..."]
                }
            }
        ],
        "metadata_limit": 1000
    }
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                   | Description                                                                                         |
| -------------- | ---------------------- | --------------------------------------------------------------------------------------------------- |
| name           | String                 | The token's name                                                                                    |
| symbol         | String                 | The token's symbol                                                                                  |
| extensions     | Vec\<ModuleDefinition> | Any Andromeda Modules to be attached to the token                                                   |
| metadata_limit | Option\<u64>           | An optional limit on the size of any metadata assigned to a minted token in the contract (in bytes) |

### UpdateAddress

Updates the contract address for a given symbol. 

{% hint style="info" %}
Only available to the token creator or contract owner.
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
    "create": {
        "symbol": "ET",
        "creator": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description              |
| ----------- | ------ | ------------------------ |
| symbol      | String | The token's symbol       |
| new_address | String | The new contract address |

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### GetAddress

Query the address of a given symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetAddress {
        symbol: String
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

| Name   | Type   | Description                                     |
| ------ | ------ | ----------------------------------------------- |
| symbol | String | The token symbol for which to query the address |

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
    "address": "terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                              |
| ------- | ------ | ---------------------------------------- |
| address | String | The contract address of the given symbol |

### ContractOwner

See [Ownership](ownership.md#querymsg).
