---
description: An ADO contract to mint, edit and query receipts containing event data.
---

# Andromeda Receipts

## Receipt

The receipt structure contains a basic vector of `CosmWasm` events.

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct Receipt {
    pub events: Vec<Event>,
}
```

| Name   | Type                                        | Description                                |
| ------ | ------------------------------------------- | ------------------------------------------ |
| events | Vec<[Event](andromeda-receipts.md#receipt)> | An array of events related to the receipt. |

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub minter: String,
    pub moderators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "minter": "terra1...",
    "moderators": ["terra1...", ...],
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                  | Description                                                             |
| ---------- | --------------------- | ----------------------------------------------------------------------- |
| minter     | String                | An address authorised to edit or mint receipts.                         |
| moderators | Option\<Vec\<String>> | An optional list of contract moderators that may edit or mint receipts. |

## ExecuteMsg

### StoreReceipt

Mints a new receipt. Only available to the contract owner or a moderator.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    StoreReceipt {
        receipt: Receipt,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "store_receipt": {
        "receipt": {
            "events": [
                {
                    "type": "tax",
                    "attributes": [
                        {
                            "key": "payment",
                            "value": "terra1...<100uluna",
                        },
                        ...
                    ]
                },
                ...
            ]
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                     | Description           |
| ------- | ---------------------------------------- | --------------------- |
| receipt | [Receipt](andromeda-receipts.md#receipt) | The receipt to store. |

### EditReceipt

Edits a receipt. Only available to the contract owner or a moderator.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum ExecuteMsg {
    EditReceipt {
        receipt_id: Uint128,
        receipt: Receipt,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "store_receipt": {
        "receipt_id": "1",
        "receipt": {
            "events": [
                {
                    "type": "tax",
                    "attributes": [
                        {
                            "key": "payment",
                            "value": "terra1...<100uluna",
                        },
                        ...
                    ]
                },
                ...
            ]
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                     | Description                    |
| ---------- | ---------------------------------------- | ------------------------------ |
| receipt    | [Receipt](andromeda-receipts.md#receipt) | The receipt to store.          |
| receipt_id | Uint128                                  | The ID of the receipt to edit. |

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### Receipt

Queries a receipt by its ID.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Receipt { receipt_id: Uint128 },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "receipt": {
        "receipt_id": "1",
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type    | Description                     |
| ---------- | ------- | ------------------------------- |
| receipt_id | Uint128 | The ID of the receipt to query. |

### ReceiptResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ReceiptResponse {
    pub receipt: Receipt,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "receipt": {
        "events": [
            {
                "type": "tax",
                "attributes": [
                    {
                        "key": "payment",
                        "value": "terra1...<100uluna",
                    },
                    ...
                ]
            },
            ...
        ]
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                     | Description       |
| ------- | ---------------------------------------- | ----------------- |
| receipt | [Receipt](andromeda-receipts.md#receipt) | The receipt data. |

### ContractInfo

Queries the contract's configuration.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    ContractInfo { },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "contract_info": {}
}
```
{% endtab %}
{% endtabs %}

### ContractInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Config {
    pub minter: String,
    pub moderators: Vec<String>,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub struct ContractInfoResponse {
    pub config: Config,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "config": {
        "minter": "terra1...",
        "moderators": ["terra1...", ...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type         | Description                               |
| ---------- | ------------ | ----------------------------------------- |
| minter     | String       | The assigned minter for the contract.     |
| moderators | Vec\<String> | The assigned moderators for the contract. |

### ContractOwner

See [Ownership](ownership.md#querymsg).
