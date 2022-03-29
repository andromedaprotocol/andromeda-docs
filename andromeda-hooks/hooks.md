---
description: >-
  This document will outline the types of hooks we have and how to implement
  them in a contract.
---

# Hooks

## Hook Types

### AndromedaHook

Hooks are contained in an `AndromedaHook` enum as shown below.

```rust
pub enum AndromedaHook {
    OnExecute {
        sender: String,
        payload: Binary,
    },
    OnFundsTransfer {
        sender: String,
        payload: Binary,
        amount: Funds,
    },
    OnTransfer {
        token_id: String,
        sender: String,
        recipient: String,
    },
}
```

`OnExecute` is called at the root `fn execute` function.

`OnFundsTransfer` is called before CW20 tokens are transferred or sent, and before a transfer gets executed for a CW721.

`OnTransfer` is called when performing a transfer for a CW721.

## Calling Hooks

### OnExecute

`OnExecute` hooks are called using the general function `module_hook`.

```rust
    pub fn module_hook<T>(
        &self,
        storage: &dyn Storage,
        querier: QuerierWrapper,
        hook_msg: AndromedaHook,
    ) -> Result<Vec<T>, ContractError>
```

In the case of `OnExecute` the return type is `Result<Vec<Response>,ContractError>` so we expect a module that implements this hook to return a binary encoded `Response`.

Here is how that looks within the `fn execute` function:

```rust
    contract.module_hook::<Response>(
        deps.storage,
        deps.querier,
        AndromedaHook::OnExecute {
            sender: info.sender.to_string(),
            payload: encode_binary(&msg)?,
        },
    )?;

```

We don’t actually store the return value as our implementation uses this hook as a check that the sender is allowed to make an execute call (Similar to a whitelist of allowed senders), so what we care about is if this call returns an error or not.

This is done by the `AddressList` module. The `payload` in this case is the `ExecuteMsg` that was called.

### OnFundsTransfer

`OnFundsTransfer` is handled differently than the `OnExecute` hook.

It is broadcast using the `on_funds_transfer` function as shown below:

```rust
pub fn on_funds_transfer(
    storage: &dyn Storage,
    querier: QuerierWrapper,
    sender: String,
    amount: Funds,
    msg: Binary,
) -> Result<(Vec<SubMsg>, Vec<Event>, Funds), ContractError>
```

{% hint style="info" %}
We can’t use the `module_hook` function as this case requires some custom logic.
{% endhint %}

The return type of each hook call is an `OnFundsTransferResponse` struct:

```rust
pub struct OnFundsTransferResponse {
    pub msgs: Vec<SubMsg>,
    pub events: Vec<Event>,
    pub leftover_funds: Funds,
}
```



| Name             | Type         | Description                                                                |
| ---------------- | ------------ | -------------------------------------------------------------------------- |
| `msgs`           | Vec\<SubMsg> | messages that the module wants to execute                                  |
| `events`         | Vec\<Event>  | any events the module wants to store. These are used to generate receipts. |
| `leftover_funds` | Funds        | The funds that are left after any deductions are made by the module        |

{% hint style="info" %}
This function also ensures that the [`Receipt`](../ado-types/receipt.md) module is invoked last if it exists, since it needs all of the previous events to create a complete receipt.
{% endhint %}

Here is an example of how this hook gets called for a `TransferAgreement:`

```rust
let (mut msgs, events, remainder) = base_contract.on_funds_transfer(
            deps.storage,
            deps.querier,
            info.sender.to_string(),
            Funds::Native(agreement.amount.clone()),
            encode_binary(&ExecuteMsg::TransferNft {
                token_id: token_id.clone(),
                recipient: recipient.clone(),
            })?,
        )?;
```

### Use

The main use case for this hook at the moment is the `Rates` module which generates sub-messages that send royalties/taxes to the correct recipient and deduct any taxes from the sent funds which is why we need `leftover_funds`. This hook is also used to generate the events needed mint receipts by the receipt module.

### OnTransfer

Similar to the `OnExecute` hook the `OnTransfer` hook is called using the general `module_hook` function.

```rust
    pub fn module_hook<T>(
        &self,
        storage: &dyn Storage,
        querier: QuerierWrapper,
        hook_msg: AndromedaHook,
    ) -> Result<Vec<T>, ContractError>
```

Here is how that looks within the `fn execute` function:

```rust
   let responses = base_contract.module_hook::<Response>(
        deps.storage,
        deps.querier,
        AndromedaHook::OnTransfer {
            token_id: token_id.clone(),
            sender: info.sender.to_string(),
            recipient: recipient.clone(),
        },
    )?;
```

### Use

The main use for the `OnTransfer` hook currently is the `Offers` module. Transferring an NFT will trigger the `OnTransfer` hook which will be sent to the `Offers` module if found. If the receiver of the NFT had an offer placed on it, then we consider that the offer has been accepted by the seller and the `AcceptOffer` is automatically executed by the contract.

## Implementation

This section will show you how to implement a hook in general, and explain the specific implementations for our modules.

As stated earlier, these hooks are broadcast as queries. Therefore, each module that needs to implement them should have the `AndrHook`(`AndromedaHook`) variant in its `QueryMsg` enum.

**For `OnExecute` and `OnTransfer`, the query should return a binary encoded `Response`.**

**For `OnFundsTransfer` the query should return a binary encoded `OnFundsTransferResponse`.**

If a given module does not support a given hook, it should return `ContractError::UnsupportedOperation`. Any other error that is returned will be bubbled up and cause the transaction to fail.   &#x20;

### AddressList Module

This module only implements the `OnExecute` hook which it uses to determine if the sender is authorized based on the whitelist or blacklist. If not authorized, it returns a `ContractError::InvalidAddress` and otherwise a `Response::default()`. Here is the specific code for it:

```rust
fn handle_andr_hook(deps: Deps, msg: AndromedaHook) -> Result<Response, ContractError> {
    match msg {
        AndromedaHook::OnExecute { sender, .. } => {
            let is_included = includes_address(deps.storage, &sender)?;
            let is_inclusive = IS_INCLUSIVE.load(deps.storage)?;
            if is_included != is_inclusive {
                Err(ContractError::InvalidAddress {})
            } else {
                Ok(Response::default())
            }
        }
        _ => Err(ContractError::UnsupportedOperation {}),
    }
}
```

### Rates Module

This module only implements the `OnFundsTransfer` hook which it uses to generate sub-messages to send any royalties/taxes to the designated recipients, create events to record the transactions, and deduct any taxes from the sent funds.

Below is the implementation:

```rust
fn handle_andromeda_hook(deps: Deps, msg: AndromedaHook) -> Result<Binary, ContractError> {
    match msg {
        AndromedaHook::OnFundsTransfer { amount, .. } => {
            encode_binary(&query_deducted_funds(deps, amount)?)
        }
        _ => Err(ContractError::UnsupportedOperation {}),
    }
}
```

### Receipt Module

This module only implements the `OnFundsTransfer` hook which it uses to generate a sub message which will create a Receipt with the given events. In this case it assumes that the `payload` is a binary encoding of `Vec<Event>` which it decodes and uses to generate the Receipt message.

Below is the implementation:

```rust
fn handle_andr_hook(env: Env, msg: AndromedaHook) -> Result<Binary, ContractError> {
    match msg {
        AndromedaHook::OnFundsTransfer {
            sender: _,
            payload,
            amount,
        } => {
            let events: Vec<Event> = parse_message(&Some(payload))?;
            let msg = generate_receipt_message(env.contract.address.to_string(), events)?;
            encode_binary(&OnFundsTransferResponse {
                msgs: vec![msg],
                leftover_funds: amount,
                events: vec![],
            })
        }
        _ => Err(ContractError::UnsupportedOperation {}),
    }
}
```

### Offers Module

This module only implements the `OnTransfer` hook which it uses to check if the `recipient` of the transfer has an offer placed on the Nft with the `code_id`. If so will execute an AcceptOffer to send the funds of the offer to the seller.&#x20;

```rust
fn handle_andr_hook(deps: Deps, env: Env, msg: AndromedaHook) -> Result<Binary, ContractError> {
    match msg {
        AndromedaHook::OnTransfer {
            token_id,
            sender,
            recipient,
        } => {
            let mut resp: Response = Response::new();
            let offer = offers().may_load(deps.storage, &token_id)?;
            if let Some(offer) = offer {
                if offer.purchaser == recipient {
                    let msg = CosmosMsg::Wasm(WasmMsg::Execute {
                        contract_addr: env.contract.address.to_string(),
                        funds: vec![],
                        msg: encode_binary(&ExecuteMsg::AcceptOffer {
                            token_id,
 // We require a recipient since the owner of the token will have
// changed once this message gets executed. Sender is assuemd to be the
// orignal owner of the token.
                            recipient: sender,
                        })?,
                    });
                    resp = resp.add_message(msg);
                }
            }

            Ok(encode_binary(&resp)?)
        }
        _ => Err(ContractError::UnsupportedOperation {}),
    }
}
```
