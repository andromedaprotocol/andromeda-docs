---
description: >-
  A generic module definition for a queryable list of addresses. Used by both
  the Whitelist and Blacklist modules.
---

# Address List

## Definition

The AddressList module **cannot** be defined directly, instead it is defined using either the [`Whitelist`](https://app.gitbook.com/o/-LSIp3xE4R-AyCUl9k\_P/s/-MdsL0ugFI5WNyjzflKI/c/Al8Wb6fICx0KwYkblGXN/modules/address-list/whitelist) or [`Blacklist`](https://app.gitbook.com/o/-LSIp3xE4R-AyCUl9k\_P/s/-MdsL0ugFI5WNyjzflKI/c/Al8Wb6fICx0KwYkblGXN/modules/address-list/blacklist) module. Both of these modules can be defined using one of the following:

* A list of moderators and a valid contract code id
* A valid `AddressList` contract address

If the module is provided a list of moderators and a valid `AddressList` code id a new contract will be instantiated alongside the ADO contract and its address recorded for use in the module's message hooks.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct AddressListModule {
    pub address: Option<String>,
    pub code_id: Option<u64>,
    pub operators: Option<Vec<String>>,
    pub inclusive: bool,
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                  | Description                                                                                                                        |
| --------- | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| address   | Option\<String>       | A valid `AddressList` contract address. Highest priority.                                                                          |
| code\_id  | Option\<u64>          | A valid `AddressList` contract code id. Must be included with the `moderators` field.                                              |
| operators | Option\<Vec\<String>> | A list of address list moderators. Must be included with the `code_id` field.                                                      |
| inclusive | bool                  | Whether or not the address list is inclusive. If `true` the address list is a whitelist, if false the address list is a blacklist. |

## Methods

### `is_authorized`

Used to determine if a given address is authorized.

```rust
impl AddressListModule {
    pub fn is_authorized(
        self, 
        deps: &DepsMut, 
        address: String
    ) -> StdResult<bool> {
        let contract_addr = self.get_contract_address(deps.storage);
        require(
            contract_addr.is_some(),
            StdError::generic_err("Address list does not have an assigned contract address"),
        )?;

        let includes_address =
            query_includes_address(deps.querier, contract_addr.unwrap(), address.clone())?;
        require(
            includes_address == self.inclusive,
            StdError::generic_err("Address is not authorized"),
        )?;

        Ok(true)
    }
}
```

## Hooks

The Address List module implements the following hooks:

### `on_instantiate`

Used to instantiate a new `AddressList` contract if the `code_id` and `moderators` fields are provided.

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
            label: String::from("Address list instantiation"),
            msg: to_binary(&AddressListInstantiateMsg {
                moderators: self.moderators.clone().unwrap(),
            })?,
        };

        let msg = SubMsg {
            msg: inst_msg.into(),
            gas_limit: None,
            id: REPLY_ADDRESS_LIST,
            reply_on: ReplyOn::Always,
        };

        res = res.add_message(msg);
    }

    Ok(res)
}
```

### `on_execute`

Used to determine if the address is authorised to interact with the contract, generates a generic error if the address is not authorised. See `QueryMsg::IncludesAddress`.

```rust
fn on_execute(
    &self, 
    deps: &DepsMut, 
    info: MessageInfo, 
    _env: Env
) -> StdResult<HookResponse> {
    self.clone().is_authorized(deps, info.sender.to_string())?;

    Ok(HookResponse::default())
}
```
