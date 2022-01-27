---
description: >-
  A module used to interact with a Receipt contract, generating receipts that
  store event information on chain
---

# Receipt

## Definition

The `Receipt` module uses the `Receipt` contract to mint receipts.  To define a `Receipt` module one of the following must be included:

* A list of moderators and a valid contract code id
* A valid `Receipt` contract address

The `Receipt` module's `generate_receipt_message` is called upon an agreed transfer, after any events related to the transfer have already been generated. This mints a receipt on the related contract and returns the minted receipt's `id` as an attribute in the response under `receipt_id`.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ReceiptModule {
    pub address: Option<String>,
    pub code_id: Option<u64>,
    pub moderators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "receipt": {
        "moderators": ["terra1...", "terra1..."],
        "code_id": 1
    }
}

//OR

{
    "receipt": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                  | Description                                                                       |
| ---------- | --------------------- | --------------------------------------------------------------------------------- |
| address    | Option\<String>       | A valid `Receipt` contract address. Highest priority.                             |
| moderators | Option\<Vec\<String>> | A valid `Receipt` contract code id. Must be included with the `moderators` field. |
| code\_id   | Option\<u64>          | A list of receipt moderators. Must be included with the `code_id` field.          |

## Methods

### `generate_receipt_message`

Generates a `CosmosMsg::Wasm` struct to mint a receipt given a set of events.

```rust
impl ReceiptModule {
    pub fn generate_receipt_message(
        self,
        storage: &dyn Storage,
        events: Vec<Event>,
    ) -> StdResult<CosmosMsg> {
        let receipt = Receipt { events };

        let contract_addr = self
            .get_contract_address(storage)
            .ok_or(StdError::generic_err(
                "Receipt module does not have an assigned address",
            ))?;

        Ok(CosmosMsg::Wasm(WasmMsg::Execute {
            contract_addr,
            msg: to_binary(&ExecuteMsg::StoreReceipt { receipt })?,
            funds: vec![],
        }))
    }
}
```

## Validation

The Receipt module has the following validation requirements:

* Must be unique
* Must include either:
  * A valid `Receipt` contract `code_id` and a list of `moderators`
  * A valid `Receipt` contract `address`

## Hooks

The Receipt module implements the following hooks:

### `on_instantiate`

Used to instantiate a new `Receipt` contract if the `code_id` and `moderators` fields are provided.

```rust
fn on_instantiate(
    &self,
    _deps: &DepsMut,
    info: MessageInfo,
    _env: Env,
) -> StdResult<HookResponse> {
    let mut res = HookResponse::default();
    if self.address.is_none() {
        let inst_msg = WasmMsg::Instantiate {
            admin: Some(info.sender.to_string()),
            code_id: self.code_id.unwrap(),
            funds: vec![],
            label: String::from("Receipt instantiation"),
            msg: to_binary(&InstantiateMsg {
                minter: info.sender.to_string(),
                moderators: self.moderators.clone(),
            })?,
        };

        let msg = SubMsg {
            msg: inst_msg.into(),
            gas_limit: None,
            id: REPLY_RECEIPT,
            reply_on: ReplyOn::Always,
        };

        res = res.add_message(msg);
    }

    Ok(res)
}
```
