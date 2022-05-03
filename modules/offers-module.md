---
description: >-
  A module used to interact with a Offers contract, allowing posting and
  accepting offers on NFTs.
---

# Offers Module

### Definition

The offers module uses the offers contract in order to allow a user can sell their NFT and for buyers to post offers for said NFT. It is created as another way to make a `TransferAgreement`.

### InstantiateMsg

```rust
pub struct InstantiateMsg {
    pub andromeda_cw721_contract: String,
}
```

| Name                       | Type   | Description                                                                                        |
| -------------------------- | ------ | -------------------------------------------------------------------------------------------------- |
| `andromeda_cw721_contract` | String | The address of the contract to give offers to. Needs to be an already instantiated cw721 contract. |

More information on the Offers contract can be found [here](../ado-classes/nft-offers.md).

## Hooks

The Offers module uses the `OnTransfer` hook which is sent whenever we execute transferring an NFT token from one address to another.

```rust
 pub enum AndromedaHook {
 OnTransfer {
        token_id: String,
        sender: String,
        recipient: String,
    }
  }
```

When the Offers contract receives the hook, it will check if the `recipient` has an offer placed on the NFT being transferred with the recorded `token_id.` The assumption is that the owner transferring the token to a user that has an offer means they want to accept that offer. If the offer is expired this message will end up failing and the transfer will not happen.

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
}r
```
