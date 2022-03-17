---
description: An ADO contract to store a queryable list of addresses.
---

# Address List

## Address List

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema, Eq)]
pub struct AddressList {
    pub moderators: Vec<String>,
}
```

| Name         | Type         | Description                                                            |
| ------------ | ------------ | ---------------------------------------------------------------------- |
| `moderators` | Vec\<String> | A list of addresses that are authorised to interact with the contract. |

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub moderators: Vec<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "moderators": ["terra1..."],
}
```
{% endtab %}
{% endtabs %}

| Name         | Type         | Description                                                            |
| ------------ | ------------ | ---------------------------------------------------------------------- |
| `moderators` | Vec\<String> | A list of addresses that are authorised to interact with the contract. |

## ExecuteMsg

### AddAddress

Adds an address to the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    AddAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "add_address": {
        "address": "terra1..."
    }    
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                     |
| ------- | ------ | ------------------------------- |
| address | String | The address to add to the list. |

### RemoveAddress

Removes an address from the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    RemoveAddress { address: String },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "remove_address": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                          |
| ------- | ------ | ------------------------------------ |
| address | String | The address to remove from the list. |

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### IncludesAddress

Query if an address is included in the address list.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    IncludesAddress{
        address: String
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "includes_address": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                               |
| ------- | ------ | ----------------------------------------- |
| address | String | The address for which to query inclusion. |

#### IncludesAddressResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema)]
pub struct IncludesAddressResponse {
    pub included: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "included": false
}
```
{% endtab %}
{% endtabs %}

| Name     | Type | Description                      |
| -------- | ---- | -------------------------------- |
| included | Bool | Whether the address is included. |

### ContractOwner

See [Ownership](ownership.md#querymsg).
