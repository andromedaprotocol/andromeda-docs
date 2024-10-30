# Lockdrop V1.0.0

## Introduction

This ADO is another part of the toolkit of allowing a user to setup their own [CW20 token](cw20-v1.0.0.md). The lockdrop ADO allows users to deposit a native token in exchange for a given CW20 token.&#x20;

There are two phases:

* The first phase is the deposit phase in which users can deposit a native denom. They can also freely withdraw in this time.
* The second phase is the withdrawal phase, in which for the first half, users can withdraw up to half of their deposit, and in the second half, the amount they can withdraw decreases linearly from 50% to 0%. Users can only withdraw once during the withdrawal phase.

After the deposit phase is over, each user gets the token in proportion to how much of the native denom they put in (claims need to be enabled first.)

**Ado\_type**: lockdrop

## InstantiateMsg

{% hint style="warning" %}
Withdrawal window should be greater than 0.

Deposit window should be greater than 0.

Withdrawal window should be less than deposit window.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub init_timestamp: MillisecondsExpiration,
    pub deposit_window: MillisecondsDuration,
    pub withdrawal_window: MillisecondsDuration,
    pub incentive_token: AndrAddr,
    pub native_denom: String,
    pub kernel_address: String,
    pub owner: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"init_timestamp":164987751339483294,
"deposit_window":600000,
"withdrawal_window":400000,
"incentive_token":"andr1...",
"native_denom":"uandr",
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name                | Type                                                                                                                                     | Description                                                                                                                                                                                                                                                                                                                   |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `init_timestamp`    | [MillisecondsExpiration](../platform-and-framework/common-types.md#milliseconds)                                                         | Timestamp in milliseconds till when deposits can be made.                                                                                                                                                                                                                                                                     |
| `deposit_window`    | [Milliseconds](../platform-and-framework/common-types.md#milliseconds)[Duration](../platform-and-framework/common-types.md#milliseconds) | Number of milliseconds for which lockup deposits will be accepted.                                                                                                                                                                                                                                                            |
| `withdrawal_window` | [Milliseconds](../platform-and-framework/common-types.md#milliseconds)[Duration](../platform-and-framework/common-types.md#milliseconds) | Number of milliseconds for which lockup withdrawals will be allowed. Cannot exceed the `deposit_window`.                                                                                                                                                                                                                      |
| `incentive_token`   | [AndrAddr](../platform-and-framework/common-types.md#andraddr)                                                                           | The CW20 token being given as incentive.                                                                                                                                                                                                                                                                                      |
| `native_denom`      | String                                                                                                                                   | The native token being deposited.                                                                                                                                                                                                                                                                                             |
| `kernel_address`    | String                                                                                                                                   | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`             | Option\<String>                                                                                                                          | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

## ExecuteMsg

### Receive

Receives CW20 tokens that will be used as incentive for the lockdrop.

{% hint style="warning" %}
The sender needs to be the CW20 contract of the incentive token (Same as the one specified in instantiation).

Needs to be sent before the withdrawal period.
{% endhint %}

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

The `msg` in `Cw20ReceiveMsg` should be a `Cw20HookMsg` of type `IncreaseIncentives`

#### IncreaseIstncentives

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw20HookMsg {
    IncreaseIncentives {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"increase_incentives":{}
}
```
{% endtab %}
{% endtabs %}

***

### DepositNative

Deposit native fund in the contract in exchange for receiving a proportion of the Cw-20 incentive tokens.

{% hint style="warning" %}
Deposit window should be open.

A single type of native coins should be attached with the same denom specified in the instantiation by `native_denom`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    DepositNative {},
 }    
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit_native":{}
}
```
{% endtab %}
{% endtabs %}

### WithdrawNative

Withdraw native funds from the lockup position.

{% hint style="warning" %}
Withdrawal window needs to be open.

The amount should not exceed the maximum withdrawal allowed.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  WithdrawNative {
        amount: Option<Uint128>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_native": {
    "amount":"1000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                              |
| -------- | ---------------- | ---------------------------------------- |
| `amount` | Option\<Uint128> | The amount of native funds to withdraw.  |

### EnableClaims

`EnableClaims` can be called by anyone after the deposit and withdrawing phases have ended.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    EnableClaims {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"enable_claims":{}
}
```
{% endtab %}
{% endtabs %}

### ClaimRewards

Claims the CW20 incentive tokens from the lockdrop.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ClaimRewards {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_rewards":{}
}
```
{% endtab %}
{% endtabs %}

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### Config

Queries the config of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(ConfigResponse)]
    Config {},
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"config":{}
}
```
{% endtab %}
{% endtabs %}

#### ConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ConfigResponse {
    pub init_timestamp: MillisecondsExpiration,
    pub deposit_window: MillisecondsDuration,
    pub withdrawal_window: MillisecondsDuration,
    pub lockdrop_incentives: Uint128,
    pub incentive_token: AndrAddr,
    pub native_denom: String,
}
```
{% endtab %}
{% endtabs %}

* **`lockdrop_incentives`** is the total amount of lockdrop incentive tokens (Cw20 tokens) to be distributed among the users.

The rest of the field definitions are the same as the one in the [InstantiateMsg](lockdrop-v1.0.0.md#instantiatemsg).

### State

Queries the current state of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(StateResponse)]
    State {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"state":{}
}
```
{% endtab %}
{% endtabs %}

#### StateResponse

{% tabs %}
{% tab title="Rust" %}
```rust
 pub struct StateResponse {
        pub total_native_locked: Uint128,
        pub are_claims_allowed: bool,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_native_locked":"100000",
"are_claims_allowed": false
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="260.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>total_native_locked</code></td><td>Uint128</td><td>Total native coins deposited at the end of Lockdrop window. This value remains unchanged post the lockdrop window.</td></tr><tr><td><code>are_claims_allowed</code></td><td>bool</td><td>Boolean value indicating if the user can withdraw their token rewards or not.</td></tr></tbody></table>

### UserInfo

Gets information for the user with the `address` specified.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(UserInfoResponse)]
     UserInfo {
        address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"user_info": {
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                           |
| --------- | ------ | ------------------------------------- |
| `address` | String | The user address to get the info for. |

#### UserInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct UserInfoResponse {
    pub total_native_locked: Uint128,
    pub total_incentives: Uint128,
    pub is_lockdrop_claimed: bool,
    pub withdrawal_flag: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_native_locked":"1000",
"total_incentives":"50000",
"is_lockdrop_claimed": true,
"withrawal_flag": true
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="284.3333333333333">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>total_native_locked</code></td><td>Uint128</td><td>Total UST amount deposited by the user across all his lockup positions.</td></tr><tr><td><code>total_incentives</code></td><td>Uint128</td><td>The total amount of incentive tokens for the user.</td></tr><tr><td><code>is_lockdrop_claimed</code></td><td>bool</td><td>Whether the lockdrop tokens have been claimed by the user or not.</td></tr><tr><td><code>wtihdrawal_flag</code></td><td>bool</td><td>Whether or not the user has withdrawn during the withdrawal phase.</td></tr></tbody></table>

### WithdrawalPercentAllowed

Returns the max withdrawable percent for a position.

{% hint style="warning" %}
The provided timestamp cannot be in the past.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      #[returns(Decimal)]
      WithdrawalPercentAllowed {
            timestamp: Option<Milliseconds>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdrawal_percent_allowed":{
    "timestamp": 1705341474
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type         | Description                                                                                                                      |
| ----------- | ------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| `timestamp` | Option\<u64> | The timestamp in milliseconds to check the maximum withdrawal allowed for. If not specified, the current timestamp will be used. |

Returns a number of type Decimal representing the percentage allowed to withdraw.

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
