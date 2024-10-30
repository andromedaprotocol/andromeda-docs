# Rate Limiting Withdrawals V1.0.0

## Introduction

The **Rate Limiting Withdrawals ADO** acts as a bank account that limits the frequency and size of an account holder's withdrawals. Only one type of coin can be used.

**Ado\_type**: rate-limiting-withdrawals

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub allowed_coin: CoinAndLimit,
    pub minimal_withdrawal_frequency: MinimumFrequency,
    pub kernel_address: String,
    pub owner: Option<String>,
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
        "time":"3600000"
        }
    },
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="337.06022340942206">Name</th><th width="231.96848920702132">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>allowed_coin</code></td><td><a href="rate-limiting-withdrawals-v1.0.0.md#coinandlimit">CoinAndLimit</a></td><td>Set the allowed coin denom and the maximum amount allowed to withdraw.</td></tr><tr><td><code>minimal_withdrawal_frequency</code></td><td><a href="rate-limiting-withdrawals-v1.0.0.md#contractandkey">MinimumFrequency</a></td><td>The time required between withdrawals. Specified in milliseconds. Cannot be 0.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

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
     Time { time: MillisecondsDuration },
     }
```

* **Time:** A time in milliseconds. For example 3600 would specify that each the user needs to wait 3600 milliseconds between withdrawals.

## ExecuteMsg

### Deposits

Deposit funds for the specified recipient.

{% hint style="warning" %}
Only the allowed coin in instantiation can be deposited.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Deposits {
        recipient: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposits":{
    "recipient":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                            |
| ----------- | --------------- | ---------------------------------------------------------------------- |
| `recipient` | Option\<String> | The owner of the deposited funds. If not set, defaults to the sender.  |

### WithdrawFunds

{% hint style="warning" %}
Enough time should pass since the last withdrawal.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
     WithdrawFunds {
        amount: Uint128,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_funds":{
    "amount":"100"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                      |
| -------- | ------- | -------------------------------- |
| `amount` | Uint128 | The amount of coins to withdraw. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

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

#### CoinAllowance

Returns a **CoinAllowance** struct.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct CoinAllowance {
    pub coin: String,
    pub limit: Uint128,
    pub minimal_withdrawal_frequency: MillisecondsDuration,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"coin":"uandr",
"limit":"50000",
"minimal_withdrawal_frequency":"3600000"
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="337.06022340942206">Name</th><th width="231.96848920702132">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>coin</code></td><td>string</td><td>The coin denom.</td></tr><tr><td><code>limit</code></td><td>Uint128</td><td>The amount allowed to withdraw per withdrawal.</td></tr><tr><td><code>minimal_withdrawal_frequency</code></td><td><a href="../platform-and-framework/common-types.md#milliseconds">MillisecondsDuration</a></td><td>The time required between withdrawals. Specified in milliseconds. </td></tr></tbody></table>

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

#### AccountDetails&#x20;

Returns an **AccountDetails** struct.

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

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
