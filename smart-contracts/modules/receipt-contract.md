---
description: An ADO contract to mint, edit and query receipts containing event data.
---

# Receipt Contract

## Introduction

The **Receipt** ADO is a smart contract that is used to print receipts for transactions done by our ADOs.&#x20;

This contract is implemented as a [module](../../modules/receipt-module/) and added to ADOs that implement modules.  When we have some transfer of funds, the receipt contract is automatically called and the `events` of the transaction are saved in the `Receipt` struct.

**Ado\_type**: receipt

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub minter: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "minter": "juno1...",
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                 |
| -------- | ------ | ------------------------------------------- |
| `minter` | String | The address authorized to mint new receipts |

## ExecuteMsg

### StoreReceipt

Creates a new receipt. Generates a receipt ID.

{% hint style="info" %}
Only executable by the assigned minter address.

Usually called automatically by the receipt module after generating the receipt.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    StoreReceipt {
        receipt: Receipt,
    }
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
                            "value": "juno1...<100uluna",
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

| Name      | Type                                     | Description           |
| --------- | ---------------------------------------- | --------------------- |
| `receipt` | [Receipt](receipt-contract.md#receipt-1) | The receipt to store. |

#### Receipt

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
                            "value": "juno1...<100uluna",
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

| Name         | Type                                     | Description                    |
| ------------ | ---------------------------------------- | ------------------------------ |
| `receipt`    | [Receipt](receipt-contract.md#receipt-1) | The receipt to edit.           |
| `receipt_id` | Uint128                                  | The ID of the receipt to edit. |

### AndrReceive

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### Receipt

Queries a receipt by its ID.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    Receipt {
     receipt_id: Uint128
      }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
    "receipt": {
        "receipt_id": "1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type    | Description                     |
| ------------ | ------- | ------------------------------- |
| `receipt_id` | Uint128 | The ID of the receipt to query. |

#### ReceiptResponse

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
                        "value": "juno1...<100uluna"
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

| Name      | Type                                     | Description       |
| --------- | ---------------------------------------- | ----------------- |
| `receipt` | [Receipt](receipt-contract.md#receipt-1) | The receipt data. |

### ContractInfo

Queries the contract's configuration.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
#[serde(rename_all = "snake_case")]
pub enum QueryMsg {
    ContractInfo {},
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
        "minter": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                  |
| -------- | ------ | -------------------------------------------- |
| `minter` | String | The address authorized to mint new receipts. |

### AndrQuery

Check[ AndrQuery](../../ado\_base/andrreceive-andrquery.md#andromedaquery).
