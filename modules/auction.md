---
description: A module used to allow the auctioning of a token.
---

# Auction

## Definition

This module instantiates an [Andromeda Auction ADO](../ado-types/auction.md). If a token is under auction then it cannot be transferred which is enforced by the `on_transfer` hook. The Auction ADO also has special permissions to create a `transfer_agreement` which is how the token will be sent to the winner of the auction.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct AuctionModule {
    pub address: Option<String>,
    pub code_id: Option<u64>,
    pub operators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
    "auction": {
        "moderators": ["terra1...", "terra1..."],
        "code_id": 1
    }
}

//OR

{
    "auction": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                  | Description                                                                       |
| ----------- | --------------------- | --------------------------------------------------------------------------------- |
| `address`   | Option\<String>       | A valid `Auction` contract address. Highest priority.                             |
| `code_id`   | Option\<u64>          | A valid `Auction` contract code id. Must be included with the `moderators` field. |
| `operators` | Option\<Vec\<String>> | A list of receipt moderators. Must be included with the `code_id` field.          |

## Validation

The Auction module has the following validation requirements:

* Must be unique
* Must include either:
  * A valid `Auction` contract `code_id` and a list of `moderators`
  * A valid `Auction` contract `address`

## Hooks

The Auction module implements the following hooks:

### `on_instantiate`

Used to instantiate a new `Auction` contract if the `code_id` and `moderators` fields are provided.

```rust
fn on_instantiate(
    &self,
    _deps: &DepsMut,
    info: MessageInfo,
    env: Env,
) -> Result<HookResponse, ContractError> {
    let mut res = HookResponse::default();
    if self.address.is_none() {
        let inst_msg = WasmMsg::Instantiate {
            admin: Some(info.sender.to_string()),
            code_id: self.code_id.unwrap(),
            funds: vec![],
            label: String::from("Auction instantiation"),
            msg: to_binary(&AuctionInstantiateMsg {
                token_addr: env.contract.address.to_string(),
            })?,
        };

        let msg = SubMsg {
            msg: inst_msg.into(),
            gas_limit: None,
            id: REPLY_AUCTION,
            reply_on: ReplyOn::Always,
        };

        res = res.add_message(msg);
    }

    Ok(res)
}
```

### `on_transfer`

Disallows token transfer if it is currently in auction.

```rust
fn on_transfer(
    &self,
    deps: &DepsMut,
    _info: MessageInfo,
    _env: Env,
    _recipient: String,
    token_id: String,
) -> Result<HookResponse, ContractError> {
    self.verify_token_not_in_auction(deps, token_id)
}
```

### `on_burn`

Disallows token burn if it is currently in auction.

```rust
fn on_burn(
    &self,
    deps: &DepsMut,
    _info: MessageInfo,
    _env: Env,
    token_id: String,
) -> Result<HookResponse, ContractError> {
    self.verify_token_not_in_auction(deps, token_id)
}
```

### `on_archive`

Disallows token archive if it is currently in auction.

```rust
fn on_archive(
    &self,
    deps: &DepsMut,
    _info: MessageInfo,
    _env: Env,
    token_id: String,
) -> Result<HookResponse, ContractError> {
    self.verify_token_not_in_auction(deps, token_id)
}
```

#### `verify_token_not_in_auction`

```rust
fn verify_token_not_in_auction(
    &self,
    deps: &DepsMut,
    token_id: String,
) -> Result<HookResponse, ContractError> {
    let query_msg = AuctionQueryMsg::LatestAuctionState { token_id };
    let contract_addr = self.get_contract_address(deps.storage).unwrap();
    let response: StdResult<AuctionStateResponse> =
        deps.querier.query(&QueryRequest::Wasm(WasmQuery::Smart {
            contract_addr,
            msg: to_binary(&query_msg)?,
        }));
    // Disallow token transfers while token is in auction.
    if let Ok(response) = response {
        if !response.claimed {
            return Err(ContractError::AuctionNotEnded {});
        }
    }
    Ok(HookResponse::default())
}t
```

####
