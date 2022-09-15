---
description: >-
  A module used to interact with a CW721-Bids contract, allowing posting and
  accepting bids on NFTs.
---

# CW721-Bids Module

### Definition

The Bids module uses the cw721-bids contract in order to allow a user to sell their NFT and for buyers to bid for said NFT. It is created as another way to make a `TransferAgreement`.

### InstantiateMsg

```rust
pub struct InstantiateMsg {
    pub andromeda_cw721_contract: String,
}
```

| Name                       | Type   | Description                                                                                             |
| -------------------------- | ------ | ------------------------------------------------------------------------------------------------------- |
| `andromeda_cw721_contract` | String | The address of the cw721 contract to allow bids on. Needs to be an already instantiated cw721 contract. |

More information on the cw721-bids contract can be found[ here](../andromeda-digital-objects/cw721-bids.md).

## Hooks

The cw721-bids module uses the `OnTransfer` hook which is sent whenever we execute transferring an NFT token from one address to another.

```rust
 pub enum AndromedaHook {
 OnTransfer {
        token_id: String,
        sender: String,
        recipient: String,
    }
  }
```

When the cw721-bids contract receives the hook, it will check if the `recipient` has an bid placed on the NFT being transferred with the recorded `token_id.` The assumption is that the owner transferring the token to a user that has a bid means they want to accept that bid. If the bid is expired, this message will end up failing and the transfer will not happen.

```rust
fn handle_andr_hook(deps: Deps, env: Env, msg: AndromedaHook) -> Result<Binary, ContractError> {
    match msg {
        AndromedaHook::OnTransfer {
            token_id,
            sender,
            recipient,
        } => {
            let mut resp: Response = Response::new();
            let bid = bids().may_load(deps.storage, &token_id)?;
            if let Some(bid) = bid {
                if bid.purchaser == recipient {
                    let msg = CosmosMsg::Wasm(WasmMsg::Execute {
                        contract_addr: env.contract.address.to_string(),
                        funds: vec![],
                        // The assumption is that the owner transfering the token to a user that has
                        // an bid means they want to accept that bid. If the bid is
                        // expired this message will end up failing and the transfer will not
                        // happen.
                        msg: encode_binary(&ExecuteMsg::AcceptBid {
                            token_id,
                            // We ensure! a recipient since the owner of the token will have
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
        _ => Ok(encode_binary(&None::<Response>)?),
    }
}
```
