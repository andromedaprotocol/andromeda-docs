# Lockdrop

## Introduction

This ADO is another part of the toolkit of allowing a user to setup their own token. The lockdrop ADO allows users to deposit a native token in exchange for a given CW20 token (like a crowdfund). This ADO was heavily inspired by the [MARS lockdrop contract](https://github.com/mars-protocol/mars-periphery/tree/main/contracts/lockdrop).&#x20;

There are two phases:

The first phase is the deposit phase in which users can deposit a native denom. They can also freely withdraw in this time.

The second phase is the withdrawal phase, in which for the first half, users can withdraw up to half of their deposit, and in the second half, the amount they can withdraw decreases linearly from 50% to 0%. Users can only withdraw once during the withdrawal phase.

After the deposit phase is over, each user gets the token in proportion to how much of the native denom they put in (claims need to be enabled first). This contract also has an optional relationship with an LP bootstrapping contract which would allow users to deposit parts of their purchased tokens and native tokens to bootstrap a liquidity pool.&#x20;

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
    pub bootstrap_contract: Option<AndrAddress>,
    pub init_timestamp: u64,
    pub deposit_window: u64,
    pub withdrawal_window: u64,
    pub incentive_token: String,
    pub native_denom: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"native_denom":"ujuno",
"deposit_window":600,
"init_timestamp":1649877513,
"incentive_token":"juno1...",
"withdrawal_window":400
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                                                   | Description                                                     |
| -------------------- | -------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| `bootstrap_contract` | Option<[AndrAddress](../platform-and-framework/common-types/recipient.md#andraddress)> | Optional bootstrap contract to be used in the second phase.     |
| `init_timestamp`     | u64                                                                                    | Timestamp till when deposits can be made.                       |
| `deposit_window`     | u64                                                                                    | Number of seconds for which lockup deposits will be accepted.   |
| `withdrawal_window`  | u64                                                                                    | Number of seconds for which lockup withdrawals will be allowed. |
| `incentive_token`    | String                                                                                 | The token being given as incentive.                             |
| `native_denom`       | String                                                                                 | The native token being deposited.                               |

## ExecuteMsg

### Receive

Receives Cw-20 tokens that will be used as incentive for the lockdrop.

{% hint style="warning" %}
The sender needs to be the Cw-20 contract of the incentive token (Same as the one specified in instantiation).

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

### DepositToBootstrap

Deposits the `amount` tokens to the bootstrap contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    DepositToBootstrap {
        amount: Option<Uint128>,
        }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit_to_bootstrap":{
    "amount":"1000"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                |
| -------- | ---------------- | ---------------------------------------------------------- |
| `amount` | Option\<Uint128> | The amount of tokens to deposit to the bootstrap contract. |

### EnableClaims

Called by the bootstrap contract when liquidity is added to the Cw20-NATIVE pool to enable cw20 token withdrawals by users. If a bootstrap contract is not used, then `EnableClaims` can be called by anyone after the deposit and withdrawing phases have ended.

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

Claims the cw-20 incentive tokens from the lockdrop.

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

### WithdrawProceeds

Called by the owner after the phases are over to withdraw all of the native tokens to the given recipient, or themselves if not specified.

{% hint style="info" %}
Only the contract owner can execute `WithdrawProceeds`.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    WithdrawProceeds {
        recipient: Option<String>,
        }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_proceeds":{
    "recipient":"juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                   |
| ----------- | --------------- | ----------------------------------------------------------------------------- |
| `recipient` | Option\<String> | The recipient of the native tokens that are withdrawn. Defaults to the owner. |

### AndrReceive

Check [AndrReceive](../platform-and-framework/ado\_base.md#andrrecieve).

## QueryMsg

### Config

Queries the config of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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
    pub bootstrap_contract_address: Option<String>,
    pub init_timestamp: u64,
    pub deposit_window: u64,
    pub withdrawal_window: u64,
    pub lockdrop_incentives: Uint128,
    pub incentive_token: String,
    pub native_denom: String,
}
```
{% endtab %}
{% endtabs %}

`lockdrop_incentives` is the total amount of lockdrop incentive tokens (Cw20\_tokens) to be distributed among the users.

The rest of the field definitions are the same as the one in the [InstantiateMsg](lockdrop.md#instantiatemsg).

### State

Queries the current state of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
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
        pub total_delegated: Uint128,
        pub are_claims_allowed: bool,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"total_native_locked":"100000",
"total_delegated":"50000",
"are_claims_allowed": false
}
```
{% endtab %}
{% endtabs %}

| Name                  | Type    | Description                                                                                                        |
| --------------------- | ------- | ------------------------------------------------------------------------------------------------------------------ |
| `total_native_locked` | Uint128 | Total native coins deposited at the end of Lockdrop window. This value remains unchanged post the lockdrop window. |
| `total_delegated`     | Uint128 | Number of Tokens deposited into the bootstrap contract.                                                            |
| `are_claims_allowed`  | bool    | Boolean value indicating if the user can withdraw their token rewards or not.                                      |

### UserInfo

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     UserInfo {
        address: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"user_info": {
    "address":"juno1..."
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
    pub delegated_incentives: Uint128,
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
"delegated_incentives":"300",
"is_lockdrop_claimed": true,
"withrawal_flag": true
}
```
{% endtab %}
{% endtabs %}

| Name                   | Type    | Description                                                                            |
| ---------------------- | ------- | -------------------------------------------------------------------------------------- |
| `total_native_locked`  | Uint128 | Total UST amount deposited by the user across all his lockup positions.                |
| `total_incentives`     | Uint128 | The total amount of incentive tokens for the user.                                     |
| `delegated_incentives` | Uint128 | Cw-20 incentive tokens deposited to the bootstrap contract for TOKEN-UST Bootstrapping |
| `is_lockdrop_claimed`  | bool    | Whether the lockdrop tokens have been claimed by the user or not.                      |
| `wtihdrawal_flag`      | bool    | Whether or not the user has withdrawn during the withdrawal phase.                     |

### WithdrawalPercentAllowed

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
      WithdrawalPercentAllowed {
            timestamp: Option<u64>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdrawal_percent_allowed":{
    "timestamp": 500
    }
}
```
{% endtab %}
{% endtabs %}

Returns a number of type Decimal representing the percentage allowed to withdraw.

### AndrQuery

Check [AndrQuery](../platform-and-framework/ado\_base.md#andrquery).
