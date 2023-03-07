# Rate Limiting Withdrawals

## Introduction

The **Rate Limiting Withdrawals ADO** acts as a bank account that limits the frequency and size of an account holder's withdrawals. Only one type of coin can be used.

The contract supports [modules](broken-reference) to extend its functionality.

**Ado\_type**: rate-limiting-withdrawals

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub allowed_coin: CoinAndLimit,
    pub minimal_withdrawal_frequency: MinimumFrequency,
    pub modules: Option<Vec<Module>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"allowed_coin":{
    "coin":"uandr",
    "limit":"500"
    },
"minimal_withdrawal_frequency":{
    "time":{
        "time":"3600"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name                           | Type                                                            | Description                                                                                                                           |
| ------------------------------ | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `allowed_coin`                 | [CoinAndLimit](rate-limiting-withdrawals.md#coinandlimit)       | Set the allowed coin denom and the maximum amount allowed to withdraw.                                                                |
| `minimal_withdrawal_frequency` | [MinimumFrequency](rate-limiting-withdrawals.md#contractandkey) | The time required between withdrawals. Specified in seconds. Cannot be 0.                                                             |
| `modules`                      | Option\<Vec\<Module>>                                           | An optional vector of Andromeda[ Modules](broken-reference) that can be attached to the contract. "address-list" module can be added. |

#### CoinAndLimit

```rust
pub struct CoinAndLimit {
    pub coin: String,
    pub limit: Uint128,
}
```

| Name    | Type    | Description                                   |
| ------- | ------- | --------------------------------------------- |
| `coin`  | String  | Sets the accepted coin denom.                 |
| `limit` | Uint128 | Sets the withdrawal limit in terms of amount. |

#### MinimumFrequency

```rust
pub enum MinimumFrequency {
     Time { time: Uint128 },
     AddressAndKey { address_and_key: ContractAndKey },
```

The minimum withdrawal frequency can be set in two ways:

* **Time:** A time in seconds. For example 3600 would specify that each the user needs to wait 3600 seconds between withdrawals.
* **AddressAndKey:** Takes the value from a primitive. Need to specify the contract address of the primitive and the key associated to the value we want.&#x20;

#### ContractAndKey

```rust
pub struct ContractAndKey {
    pub contract_address: String,
    pub key: Option<String>,
}
```

| Name               | Type            | Description                                                                       |
| ------------------ | --------------- | --------------------------------------------------------------------------------- |
| `contract_address` | String          | The contract address of the primitive to get the value from.                      |
| `key`              | Option\<String> | The key of the saved value we want to use. Uses the default key if not specified. |

## ExecuteMsg

### Deposit

Deposit funds for the specified recipient.

{% hint style="warning" %}
Only the allowed coin in instantiation can be deposited.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Deposit {
        recipient: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit":{
    "recipient":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                            |
| ----------- | --------------- | ---------------------------------------------------------------------- |
| `recipient` | Option\<String> | The owner of the deposited funds. If not set, defaults to the sender.  |

### Withdraw

{% hint style="warning" %}
Enough time should pass since the last withdrawal.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
     Withdraw {
        amount: Uint128,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw":{
    "amount":"100"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                      |
| -------- | ------- | -------------------------------- |
| `amount` | Uint128 | The amount of coins to withdraw. |

### AndrReceive

{% hint style="info" %}
Uses the modules feature.
{% endhint %}

The rest of the executes can be found in the [`AndrReceive`](../../platform-and-framework/ado-base.md#andrrecieve) section.

## QueryMsg

### CoinAllowanceDetails

Provides the allowed coin and limits for withdrawal size and frequency.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
    #[returns(CoinAllowance)]
    CoinAllowanceDetails {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"coin_allowed_details":{}
}
```
{% endtab %}
{% endtabs %}

#### Response

Returns a CoinAllowance struct.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct CoinAllowance {
    pub coin: String,
    pub limit: Uint128,
    pub minimal_withdrawal_frequency: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"coin":"uandr",
"limit":"50000",
"minimal_withdrawal_frequency":"3600"
}
```
{% endtab %}
{% endtabs %}

| Name                           | Type    | Description                                                   |
| ------------------------------ | ------- | ------------------------------------------------------------- |
| `coin`                         | string  | The coin denom.                                               |
| `limit`                        | Uint128 | The amount allowed to withdraw per withdrawal.                |
| `minimal_withdrawal_frequency` | Uint128 | The time required between withdrawals. Specified in seconds.  |

### AccountDetails

Shows the balance and latest withdrawal time.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
     #[returns(AccountDetails)]
     AccountDetails {
        account: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"account_details":{
    "account":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                           |
| --------- | ------ | ------------------------------------- |
| `account` | String | The address to check the account for. |

#### Response&#x20;

Returns an AccountDetails struct.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AccountDetails {
    pub balance: Uint128,
    pub latest_withdrawal: Option<Timestamp>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"balance":"100000",
"latest_withdrawal":"1658744585559151746"
}
```
{% endtab %}
{% endtabs %}

| Name                | Type               | Description                                               |
| ------------------- | ------------------ | --------------------------------------------------------- |
| `balance`           | Uint128            | The balance of the specified address.                     |
| `latest_withdrawal` | Option\<Timestamp> | The time of the last withdrawal of the specified address. |

### AndrQuery

A set of base queries common to all Andromeda ADOs. Check[ AndrQuery](../../platform-and-framework/ado-base.md#andrquery).
