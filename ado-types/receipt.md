---
description: An ADO contract to mint, edit and query receipts containing event data.
---

# Receipt

## Receipt

The receipt structure contains a basic vector of `CosmWasm` events.

```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct Receipt {
    pub events: Vec<Event>,
}
```

| Name     | Type                                                                              | Description                                |
| -------- | --------------------------------------------------------------------------------- | ------------------------------------------ |
| `events` | Vec<[Event](https://docs.rs/cosmwasm-std/0.16.2/cosmwasm\_std/struct.Event.html)> | An array of events related to the receipt. |

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub minter: String,
    pub operators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "minter": "terra1...",
    "operators": ["terra1...", ...],
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                  | Description                                                                                      |
| ----------- | --------------------- | ------------------------------------------------------------------------------------------------ |
| `minter`    | String                | The address authorized to mint new receipts                                                      |
| `operators` | Option\<Vec\<String>> | Optional list of moderating addresses authorized to update receipts, defaults to an empty vector |

## ExecuteMsg

### StoreReceipt

Mint a new receipt. Only executable by the assigned `minter` address. Generates a receipt ID.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
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

| Name      | Type                          | Description           |
| --------- | ----------------------------- | --------------------- |
| `receipt` | [Receipt](receipt.md#receipt) | The receipt to store. |

### EditReceipt

Edit a receipt by ID. Only executable by the assigned `minter` address or a valid `operator`.

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
    "edit_receipt": {
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

| Name         | Type                          | Description                    |
| ------------ | ----------------------------- | ------------------------------ |
| `receipt`    | [Receipt](receipt.md#receipt) | The receipt to edit.           |
| `receipt_id` | Uint128                       | The ID of the receipt to edit. |

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

| Name        | Type    | Description                     |
| ----------- | ------- | ------------------------------- |
| receipt\_id | Uint128 | The ID of the receipt to query. |

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

| Name      | Type                          | Description       |
| --------- | ----------------------------- | ----------------- |
| `receipt` | [Receipt](receipt.md#receipt) | The receipt data. |

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
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                  |
| -------- | ------ | -------------------------------------------- |
| `minter` | String | The address authorized to mint new receipts. |
