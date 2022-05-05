# Swapper

## Introduction

The **Swapper** ADO allows us to add swapping functionality to any [mission](../mission.md) very easily, along with being able to choose the Automated Market Maker(AMM) that does the swapping.

This is done by implementing a common `Swap` message on each possible engine such that the `Swapper` will not care what the underlying contract is. Currently, the only swapping engine that can be used is Astroport, but we intend to add more in the future.

#### Example:

The functionality is best explained with an example. Suppose we had an [`Anchor`](anchor.md) contract that expects UST to be deposited. We also want to support LUNA deposits. This can be done by setting up a `Swapper` in a mission between the `Rates` and `Anchor` contracts with an `ask_asset` of UST. If the `Swapper` receives LUNA, or any other asset, it will swap it to UST and send it along to the `Anchor` contract. If it receives UST, it will send that as is to the `Anchor` contract without swapping. So what we have achieved here is effectively a normalization of assets: many types can go in and only a single one comes out.

The engine implementation in Astroport uses the [`Router` ](https://docs.astroport.fi/astroport/smart-contracts/router)contract Astroport has provided.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub swapper_impl: SwapperImpl,
    pub primitive_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"swaper_impl":{
  "reference":{
      "identifier":"terra1..."
      },
   "primitive_contract":"terra1..."
 }
```
{% endtab %}
{% endtabs %}

| Name                 | Type        | Description                                                                          |
| -------------------- | ----------- | ------------------------------------------------------------------------------------ |
| `swapper_impl`       | SwapperImpl | The contract to use as the swapping engine. Currently, only Astroport is supported.  |
| `primitive_contract` | String      | The primitive contract use to store the factory contract  if used.                   |

#### SwapperImpl

```rust
pub enum SwapperImpl {
    New(InstantiateInfo),
    Reference(AndrAddress),
}
```

* **New**: Specifies the instantiation specification for the swapper implementation
* **Reference**: Specifies the swapper implementation by reference to an existing contract.    (Check [AndrAddress](../common-types/recipient.md#andraddress))

#### InstantiateInfo

```rust
pub struct InstantiateInfo {
    pub msg: Binary,
    pub ado_type: String,
}
```

| Name       | Type   | Description                                 |
| ---------- | ------ | ------------------------------------------- |
| `msg`      | Binary | The instantiate message encoded in base64.  |
| `ado_type` | String | The ADO type. Used to retrieve the code id. |

## ExecuteMsg

### Receive

Receive Cw-20 tokens with a swap message attached.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  Receive(Cw20ReceiveMsg)
}
```
{% endtab %}
{% endtabs %}

#### Cw20ReceiveMsg

```rust
pub struct Cw20ReceiveMsg {
    pub sender: String,
    pub amount: Uint128,
    pub msg: Binary,
}
```

The `msg` field in `Cw20ReceiveMsg` should be a `Cw20HookMsg` of type `swap`.

The binary message should be base64 encoded and not raw binary.

#### Cw20HookMsg

```rust
pub enum Cw20HookMsg {
    Swap {
        ask_asset_info: AssetInfo,
        recipient: Option<Recipient>,
    },
}
```

### Swap

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{ 
  Swap {
        ask_asset_info: AssetInfo,
        recipient: Option<Recipient>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"swap":{
"ask_asset_info":{
   "cw20":"terra1..."
    },
"recipient"{
    "addr":"terra1..."
     }
  }
```
{% endtab %}
{% endtabs %}

| Name             | Type                                              | Description                                                                                                             |
| ---------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `ask_asset_info` | AssetInfo                                         | The asset to be converted to. If we want to convert any sent funds to 'UST' the we would define `ask_asset_info` as UST |
| `recipient`      | Option<[Recipient](../common-types/recipient.md)> | The recipient of the swapped funds.                                                                                     |

#### AssetInfo

Represents an asset info instance containing only verified data which is saved in the contract storage.

```rust
pub type AssetInfo = AssetInfoBase<Addr>
```

#### AssetInfoBase

```rust
pub enum AssetInfoBase<T> {
    Cw20(T),
    Native(String),
}
```

Represents the type of an fungible asset. Each asset info instance can be one of two variants:&#x20;

* **CW20 tokens**: To create an **** asset info instance of this type, provide the contract address of the token.
* &#x20;**Native SDK coins**: To create an asset info instance of this type, provide the denomination.

### Send

Send is an **internal** function that sends funds to the recipient.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   Send {
        ask_asset_info: AssetInfo,
        recipient: Recipient,
    }
 }
```
{% endtab %}
{% endtabs %}

| Name             | Type                                      | Description                                                                                                             |
| ---------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `ask_info_asset` | AssetInfo                                 | The asset to be converted to. If we want to convert any sent funds to 'UST' the we would define `ask_asset_info` as UST |
| `recipient`      | [Recipient](../common-types/recipient.md) | The recipient of the swapped funds.                                                                                     |

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### SwapperImpl

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg { 
     SwapperImpl {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"swaper_iml":{}
}
```
{% endtab %}
{% endtabs %}

Returns an [AndrAddress](../common-types/recipient.md#andraddress) for the swapper implementation contract.

### AndrQuery

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
