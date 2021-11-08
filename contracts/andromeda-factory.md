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

| Name                    | Type | Description                                                            |
| ----------------------- | ---- | ---------------------------------------------------------------------- |
| token\_code\_id         | u64  | The code ID for the token contract                                     |
| receipt\_code\_id       | u64  | The code ID for the [receipt](andromeda-receipts.md) contract          |
| address\_list\_code\_id | u64  | The code ID for the [address list](andromeda-address-list.md) contract |

## ExecuteMsg

### Create

Creates a new token contract using the defined  `token_code_id`. The address of the instantiated contract is then registered under the token's symbol. The sender of the message is defined as the minter for the token contract and as such, the owner of the token contract.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    Create {
        name: String,
        symbol: String,
        modules: Vec<ModuleDefinition>,
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
        "modules": [
            {
                "whitelist": {
                    "moderators": ["terra1..."]
                }
            },
            { 
                "taxable": {
                    "rate": {
                        "flat": {
                            "amount": 2,
                            "denom": "uluna"
                        }
                    },
                    "receivers": ["terra1...", "terra1..."],
                    "description": "Some tax payment to be made to..."
                }
            }
        ],
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                   | Description                                       |
| ------- | ---------------------- | ------------------------------------------------- |
| name    | String                 | The token's name                                  |
| symbol  | String                 | The token's symbol                                |
| modules | Vec\<ModuleDefinition> | Any Andromeda Modules to be attached to the token |

### UpdateAddress

Updates the contract address for a given symbol.&#x20;

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
    "create": {
        "symbol": "ET",
        "creator": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description              |
| ------------ | ------ | ------------------------ |
| symbol       | String | The token's symbol       |
| new\_address | String | The new contract address |

### UpdateCodeId

Updates the contract address for a given symbol.&#x20;

{% hint style="warning" %}
Only available to the contract owner.



One of the code IDs must be provided.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    UpdateCodeId {
        receipt_code_id: Option<u64>,
        address_list_code_id: Option<u64>,
        token_code_id: Option<u64>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_code_id": {
        "receipt_code_id": 1,
        "address_list_code_id": 2,
    }
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type         | Description                                                                       |
| ----------------------- | ------------ | --------------------------------------------------------------------------------- |
| receipt\_code\_id       | Option\<u64> | Optional new code ID for the [receipt](andromeda-receipts.md) contracts.          |
| token\_code\_id         | Option\<u64> | Optional new code ID for the [token](andromeda-digital-object.md) contracts.      |
| address\_list\_code\_id | Option\<u64> | Optional new code ID for the [address list](andromeda-address-list.md) contracts. |

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

### CodeIds

Query the address of a given symbol.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    CodeIds {}
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "code_ids": {}
}
```
{% endtab %}
{% endtabs %}

#### CodeIdsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct CodeIdsResponse {
    pub receipt_code_id: u64,
    pub token_code_id: u64,
    pub address_list_code_id: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "receipt_code_id": 1,
    "token_code_id": 2,
    "address_list_code_id": 3,
}
```
{% endtab %}
{% endtabs %}

| Name                    | Type | Description                                                         |
| ----------------------- | ---- | ------------------------------------------------------------------- |
| receipt\_code\_id       | u64  | The code ID used for [receipt](andromeda-receipts.md) ADOs          |
| token\_code\_id         | u64  | The code ID used for [token](andromeda-digital-object.md) ADOs      |
| address\_list\_code\_id | u64  | The code ID used for [address list](andromeda-address-list.md) ADOs |

### ContractOwner

See [Ownership](ownership.md#querymsg).
