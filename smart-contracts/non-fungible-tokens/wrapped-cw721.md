# Wrapped-CW721

## Introduction

The `Wrapped-Cw721` ADO is a smart contract that wraps cw721 tokens. This makes it possible to "upgrade" existing cw721 tokens to leverage our modules. One simple example is if a user wants to leverage our `TransferAgreement` functionality to make a trade without the need of a marketplace or escrow service. In this case they can wrap the token, sell the wrapped token, and then the new owner can unwrap to get the original.

The user can deposit any cw721 token and get back a "wrapped" version which can utilize our modules. They can also be "unwrapped" back to  the underlying token by depositing the wrapped token in the contract (the creator of the contract can specify if unwrapping should be allowed or not as there are some instances where they may want to permanently wrap a token).

**Ado\_type**: wrapped-cw721

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub primitive_contract: String,
    pub cw721_instantiate_type: InstantiateType,
    pub can_unwrap: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"primitive_contract":"juno1...",
"cw721_instantiate_type":{
  "address":"juno1..."
   },
 "can_unwrap": true
}
```
{% endtab %}
{% endtabs %}

| Name                     | Type            | Description                                                                                                                                                           |
| ------------------------ | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `primitive_contract`     | String          | The primitive contract use to store the factory contract  if used.                                                                                                    |
| `cw721_instantiate_type` | InstantiateType | The cw721 contract can be instantiated or an existing address can be used. In the case that  an existing address is used, the minter must be set to be this contract. |
| `can_unwrap`             | bool            | Whether or not the cw721 token can be unwrapped once it is wrapped.                                                                                                   |

#### InstantiateType

```rust
pub enum InstantiateType {
    New(Cw721Specification),
    Address(String),
}
```

* **New**: A new contract that would be instantiated using the `Cw721Specification` struct. The minter for the Cw721 contract is automatically defined as this contract.
* **Address**: An existing contract that will be referenced by the contract address.

#### Cw721Specification

```rust
pub struct Cw721Specification {
    pub name: String,
    pub symbol: String,
    pub modules: Option<Vec<Module>>,
}
```

| Name      | Type                                                        | Description                                                                                                               |
| --------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `name`    | String                                                      | Name of the NFT.                                                                                                          |
| `symbol`  | String                                                      | Symbol of the NFT.                                                                                                        |
| `modules` | Option\<Vec<[Module](../../modules/module-definitions.md)>> | An optional vector of Andromeda Modules. The module definitions can be found[ here](../../modules/module-definitions.md). |

## ExecuteMsg

### ReceiveNft

Receives the NFT sent by the user from the CW721 contract defined in the `InstantiateMsg`. When sending an NFT to this contract, the user would send a message to either wrap the sent token, or unwrap it (If allowed). This will be defined depending on the `Cw721HookMsg` attached to the Cw721ReceiveMsg.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ReceiveNft(Cw721ReceiveMsg),
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"receive_nft":{
"cw721_receive_msg":{
"sender":"juno1...",
"token_id":"my_token_id",
"msg":"sakdfKSDGFH978sDF7sDGdsf"
   }
}
```
{% endtab %}
{% endtabs %}

#### Cw721ReceiveMsg

```rust
pub struct Cw721ReceiveMsg {
    pub sender: String,
    pub token_id: String,
    pub msg: Binary,
}
```

The `msg` in the `Cw721ReceiveMsg` should be a base64 encoded binary of a  `Cw721HookMsg`.

#### Cw721HookMsg

```rust
pub enum Cw721HookMsg {
    Wrap { wrapped_token_id: Option<String> },
    Unwrap {},
}
```

### AndrReceive&#x20;

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### AndrQuery

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery).
