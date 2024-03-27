---
description: Defining recurring structs used by our ADOs.
---

# Common Types

This section contains the definitions of structures used by many of our ADOs. To avoid redefining them every time, they will be placed in this section and referenced.&#x20;

## Coin

### Definition <a href="#definition" id="definition"></a>

A struct used to store the denom and amount of funds.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct Coin {
    pub denom: String,
    pub amount: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"denom":"uandr",
"amount":"1000000"
}
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                    |
| -------- | ------- | ------------------------------ |
| `denom`  | String  | The denomination of the funds. |
| `amount` | Uint128 | The amount of funds.           |

## Expiration

### Definition

Expiration represents a point in time when some event happens. It can compare with a BlockInfo and will return **is\_expired() == true** once the condition is hit (and for every block in the future).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Expiration {
    AtHeight(u64),
    AtTime(Timestamp),
    Never {},
}
```
{% endtab %}
{% endtabs %}

**AtHeight:** AtHeight will expire when `env.block.height` >= height.

**AtTime:** AtTime will expire when `env.block.time` >= time.

**Never:** Never will never expire. Used to express the empty variant.

#### Timestamp

A point in time in **nanosecond** precision.

```rust
pub struct Timestamp(Uint64)
```

#### JSON Implementation

```json
{
"expiration":{
  "at_height": 500
  }

or

{
"expiration":{
  "at_time":"1246593483949835832"
}
```

## Milliseconds

Struct that represents time in milliseconds in u64.

```rust
#[cw_serde]
#[derive(Default, Eq, PartialOrd, Copy)]
pub struct Milliseconds(pub u64);
```

## Recipient

A simple struct used for inter-contract communication:

```rust
pub struct Recipient {
    pub address: AndrAddr,
    pub msg: Option<Binary>,
    pub ibc_recovery_address: Option<AndrAddr>,
}
```

The struct can be used in two ways:

1\. Simply just providing an `AndrAddr` which will treat the communication as a transfer of any related funds.

&#x20;2\. Providing an `AndrAddr` and a `Binary` message which will be sent to the contract at the resolved address. The `Binary` message can be any message that the contract at the resolved address can handle.

The `ibc_recovery_address` is an address to receive funds in case the IBC message failed.

### AndrAddr

An address that can be used within the Andromeda ecosystem.

```rust
pub struct AndrAddr(String);
```

**The address can be one of two things:**

* A valid human readable address e.g. "andr1..."
* A valid Andromeda [Virtual File System](andromeda-messaging-protocol/virtual-file-system.md) (VFS) path e.g. "/home/user/app/component"

VFS paths can be local in the case of an app and can be done by referencing  `./component` .

They can also contain protocols for cross chain communication and are structured in the following way:

{% hint style="danger" %}
The chain is required if IBC is to be used.&#x20;

VFS paths are required in specifying the recipients in IBC messages.
{% endhint %}

`<protocol>://<chain>/<path>` e.g. `ibc://stargaze/home/user/app/component`
