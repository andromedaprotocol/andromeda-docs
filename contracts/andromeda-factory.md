---
description: The message definitions for the Andromeda Factory contract
---

# Andromeda Factory

### InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub token_code_id: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "token_code_id": 1
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| token\_code\_id | String | The code ID for the token contract |

### ExecuteMsg

#### Create

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

| Name | Type | Description |
| :--- | :--- | :--- |
| name | String | The token's name |
| symbol | String | The token's symbol |
| extensions | Vec&lt;ModuleDefinition&gt; | Any Andromeda Modules to be attached to the token |
| metadata\_limit | Option&lt;u64&gt; | An optional limit on the size of any metadata assigned to a minted token in the contract \(in bytes\) |

#### TokenCreationHook

A hook message used by the contract to assign an address to a given symbol.

{% hint style="info" %}
Called automatically upon sending a `Create` message in order to register the newly instantiated contract's address.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    TokenCreationHook {
        symbol: String,
        creator: String,
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

| Name | Type | Description |
| :--- | :--- | :--- |
| symbol | String | The token's symbol |
| creator | String | The address for the creator of the token |

#### UpdateAddress

Updates the contract address for a given symbol.

{% hint style="danger" %}
Only available to the token creator.
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

| Name | Type | Description |
| :--- | :--- | :--- |
| symbol | String | The token's symbol |
| new\_address | String | The new contract address |

### QueryMsg

#### GetAddress

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

| Name | Type | Description |
| :--- | :--- | :--- |
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

| Name | Type | Description |
| :--- | :--- | :--- |
| address | String | The contract address of the given symbol |

