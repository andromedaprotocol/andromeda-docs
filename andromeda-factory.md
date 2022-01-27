---
description: A factory contract for generating Andromeda Digital Objects
---

# Factory

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
| token\_code\_id         | u64  | The code ID for the ADO  contract                                      |
| receipt\_code\_id       | u64  | The code ID for the [receipt](ado-types/receipt.md) contract           |
| address\_list\_code\_id | u64  | The code ID for the [address list](ado-types/address-list.md) contract |

## ExecuteMsg

### Create

Creates a new ADO contract using the defined `token_code_id`. The address of the instantiated contract is then registered under the ADO's symbol. The sender of the message is defined as the minter for the ADO contract and as such, the owner of the ADO contract.

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

| Name    | Type                   | Description                                     |
| ------- | ---------------------- | ----------------------------------------------- |
| name    | String                 | The ADO's name                                  |
| symbol  | String                 | The ADO's symbol                                |
| modules | Vec\<ModuleDefinition> | Any Andromeda Modules to be attached to the ADO |

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
    "update_address": {
        "symbol": "ET",
        "new_address" : "terra1..."
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
| receipt\_code\_id       | Option\<u64> | Optional new code ID for the [receipt](ado-types/receipt.md) contracts.           |
| token\_code\_id         | Option\<u64> | Optional new code ID for the [ADO](andromeda-digital-object.md) contracts.        |
| address\_list\_code\_id | Option\<u64> | Optional new code ID for the [address list](ado-types/address-list.md) contracts. |

### UpdateOwner

See [Ownership](ado-types/ownership.md#executemsg).

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

| Name   | Type   | Description                                   |
| ------ | ------ | --------------------------------------------- |
| symbol | String | The ADO symbol for which to query the address |

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

| Name    | Type   | Description                                  |
| ------- | ------ | -------------------------------------------- |
| address | String | The contract address of the given ADO symbol |

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
| receipt\_code\_id       | u64  | The code ID used for [receipt](ado-types/receipt.md) ADOs           |
| token\_code\_id         | u64  | The code ID used for the [ADOs](andromeda-digital-object.md)        |
| address\_list\_code\_id | u64  | The code ID used for [address list](ado-types/address-list.md) ADOs |

### ContractOwner

See [Ownership](ado-types/ownership.md#querymsg).
