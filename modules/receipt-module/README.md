---
description: >-
  A module used to interact with a Receipt contract, generating receipts that
  store event information on chain.
---

# Receipt Module

## Definition

The `Receipt` module creates a [receipt ](../../andromeda-digital-objects/receipt.md)contract to generate receipts that store the events activity.

### InstantiateMsg

The Instantiate message for the receipt contract.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub minter: String,
}
```

| Name     | Type   | Description                                  |
| -------- | ------ | -------------------------------------------- |
| `minter` | String | The address authorized to mint new receipts. |

More information on the Receipt contract can be found [here](../../andromeda-digital-objects/receipt.md).

## Hooks

When we have a transfer of funds of some sort we call the `OnFundsTransfer` function to send the provided hook message to all registered modules.&#x20;

```rust
pub fn on_funds_transfer(
        &self,
        storage: &dyn Storage,
        querier: QuerierWrapper,
        sender: String,
        amount: Funds,
        msg: Binary,
    ) -> Result<(Vec<SubMsg>, Vec<Event>, Funds), ContractError> {
        let modules: Vec<ModuleInfoWithAddress> = self.load_modules_with_address(storage)?;
        let mut remainder = amount;
        let mut msgs: Vec<SubMsg> = Vec::new();
        let mut events: Vec<Event> = Vec::new();
        let mut receipt_module_address: Option<String> = None;
        for module in modules {
            if module.module.module_type == RECEIPT {
                // If receipt module exists we want to make sure we do it last.
                receipt_module_address = Some(module.address.clone());
                continue;
            }
            let mod_resp: Option<OnFundsTransferResponse> = hook_query(
                querier,
                AndromedaHook::OnFundsTransfer {
                    payload: msg.clone(),
                    sender: sender.clone(),
                    amount: remainder.clone(),
                },
                module.address.clone(),
            )?;
            if let Some(mod_resp) = mod_resp {
                remainder = mod_resp.leftover_funds;
                msgs = [msgs, mod_resp.msgs].concat();
                events = [events, mod_resp.events].concat();
            }
        }
        if let Some(receipt_module_address) = receipt_module_address {
            let mod_resp: Option<OnFundsTransferResponse> = hook_query(
                querier,
                AndromedaHook::OnFundsTransfer {
                    payload: to_binary(&events)?,
                    sender,
                    amount: remainder.clone(),
                },
                receipt_module_address,
            )?;
            if let Some(mod_resp) = mod_resp {
                msgs = [msgs, mod_resp.msgs].concat();
                events = [events, mod_resp.events].concat();
            }
        }

        Ok((msgs, events, remainder))
    }
}
```

If a receipt module is found it is sent the following hook:

```rust
 pub enum AndromedaHook{
   OnFundsTransfer {
        sender: String,
        payload: Binary,
        amount: Funds,
    }
  }
```

When the receipt module receives the `OnFundsTranfer` hook, it calls on `generate_receipt_message` function to mint a receipt from the given set of events. These events are generated when the transfer of funds occurs and are stored in the payload of the `OnFundsTransfer` hook.

```rust
pub fn generate_receipt_message(
    contract_addr: String,
    events: Vec<Event>,
) -> Result<SubMsg, ContractError> {
    let receipt = Receipt { events };

    Ok(SubMsg::new(CosmosMsg::Wasm(WasmMsg::Execute {
        contract_addr,
        msg: to_binary(&ExecuteMsg::StoreReceipt { receipt })?,
        funds: vec![],
    })))
}

```
