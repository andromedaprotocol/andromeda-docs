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
"denom":"ujuno",
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

Expiration represents a point in time when some event happens. It can compare with a BlockInfo and will return is\_expired() == true once the condition is hit (and for every block in the future).

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

A point in time in nanosecond precision.

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
  "at_time":"124655832"
}

```

## Recipient

### Definition

When building ADOs, the recipient of a function/message could be another ADO or an address. This is why we use the Recipient enum to differentiate between a user recipient and an ADO recipient.

The `Recipient` enum looks as follows:&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Recipient {
    Addr(String),
    ADO(ADORecipient),
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"addr":"juno1..."
}

or

{
"a_d_o":{
    "address":{
        "identifier":" splitter contract"
        }
    }
}
    
```
{% endtab %}
{% endtabs %}

```rust
pub enum Recipient {
    Addr(String),
    ADO(ADORecipient),
}
```

**Addr:** When the recipient address is not another ADO. It is assumed that it is a valid address.

**ADO:** When the recipient address is another ADO. Uses the `ADORecipient` struct defined below.

#### **ADORecipient**

The address can be either a contract address or the human-readable identifier used in an app contract.

```rust
pub struct ADORecipient {
    pub address: AndrAddress,
    pub msg: Option<Binary>,
}
```

| Name      | Type            | Description                                      |
| --------- | --------------- | ------------------------------------------------ |
| `address` | AndrAddress     | Check [AndrAdress](common-types.md#andradress)   |
| `msg`     | Option\<Binary> | An optional message to attach for the recipient. |

### AndrAddress

A struct used to reference another ADO contract. Can be either an address or the name of an ADO component in an app.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AndrAddress {
    pub identifier: String,
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"identifier":"juno1..."
}

or

{
"identifier":"component-name"
}
```
{% endtab %}
{% endtabs %}
