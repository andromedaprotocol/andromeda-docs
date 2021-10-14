---
description: An ADO contract to hold funds for a set period of time.
---

# Andromeda Timelock

## Escrow

The time lock contract uses a basic struct to store a record of funds being held.

```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct Escrow {
    pub coins: Vec<Coin>,
    pub expiration: Expiration,
    pub recipient: String,
}
```

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub address_list: Option<AddressListModule>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "address_list": {
        "code_id": 1,
        "moderators": ["terra1..."],
        "inclusive": true
    }
}

//OR

{
    "address_list": {
        "address": "terra1...",
        "inclusive": true
    }
}

//OR

{
    "address_list": null
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                  | Description                                                       |
| ------------ | ----------------------------------------------------- | ----------------------------------------------------------------- |
| address_list | Option<[AddressListModule](../modules/address-list/)> | An optional address list to use when calling any execute methods. |

## ExecuteMsg

### HoldFunds

Holds sent funds in escrow.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    HoldFunds {
        expiration: Expiration,
        recipient: Option<String>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "hold_funds": {
        "recipient": "terra1",
        "expiration": {
            "at_block": 1
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type            | Description                                                    |
| ---------- | --------------- | -------------------------------------------------------------- |
| recipient  | Option\<String> | Optional recipient address. If not set defaults to the sender. |
| expiration | Expiration      | When the funds can be released.                                |

### ReleaseFunds

Releases any held funds for the sender.

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub enum ExecuteMsg {
    ReleaseFunds {},
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "release_funds": {
    }
}
```
{% endtab %}
{% endtabs %}

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### GetLockedFunds

Query any held funds for an address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetLockedFunds{
        address: String
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_locked_funds": {
        "address": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                                  |
| ------- | ------ | -------------------------------------------- |
| address | String | The address for which to query locked funds. |

#### GetLockedFundsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct GetLockedFundsResponse {
    pub funds: Option<Escrow>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "funds": {
        "coins": {
            "uluna": 1000
        },
        "expiration": {
            "at_block": 1,
        },
        "recipient": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type                                   | Description     |
| ----- | -------------------------------------- | --------------- |
| funds | [Escrow](andromeda-timelock.md#escrow) | The held funds. |

### GetTimelockConfig

Queries the contract's configuration.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    GetTimelockConfig {
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "get_timelock_config": {
    }
}
```
{% endtab %}
{% endtabs %}

#### GetTimelockConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct GetTimelockConfigResponse {
    pub address_list: Option<AddressListModule>,
    pub address_list_contract: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "address_list": {
        "address": "terra1..."
    },
    "address_list_contract": "terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                  | Type                                                             | Description                                          |
| --------------------- | ---------------------------------------------------------------- | ---------------------------------------------------- |
| address_list          | Option<[AddressListModule](../modules/address-list/#definition)> | The `AddressList` module definition.                 |
| address_list_contract | Option\<String>                                                  | The contract address for the `AddressList` contract. |

### ContractOwner

See [Ownership](ownership.md#querymsg).
