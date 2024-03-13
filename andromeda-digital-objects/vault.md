# Vault

## Introduction

The purpose of the vault ADO is to provide central funds for an [App](../smart-contracts/andromeda-apps/app.md). This means, it will allow the app to deposit funds to the vault, and these funds can be proxied to various yield strategies.

{% hint style="warning" %}
Since the Terra crash, no yield strategies are currently available.

Vaults accept funds from anyone and not only apps, but are usually implemented to manage app funds.&#x20;
{% endhint %}

Funds can be deposited to the vault and a record of the deposited funds is stored. Then strategies can be added by the contract owner at any point by calling `UpdateStrategy`.

Deposits can be made to yield strategies in partial fashion, sending part of what you would like to deposit and taking the rest from what the vault is currently holding. For example, a wallet could have 10 ANDR held within the vault, and they wish to deposit 50 ANDR to a yield strategy, in this case they can send 40 ANDR with a deposit message and 10 ANDR will be removed from their deposits and sent to the yield strategy contract.

Has the "withdraw" feature enabled.

**Ado\_type**: vault

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1...",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `kernel_address` | String          | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

## ExecuteMsg

### Deposit

Deposits funds to the vault or a strategy.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Deposit {
        recipient: Option<AndrAddr>,
        msg: Option<Binary>
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

| Name        | Type                                                                   | Description                                                                             |
| ----------- | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `recipient` | Option<[AndrAddr](../platform-and-framework/common-types.md#andraddr)> | The recipient of the deposit. Defaults to the sender if not specified.                  |
| `msg`       | Optiony\<Binary>                                                       | An optional base64 binary encoded message to specify the deposit options.               |
| `strategy`  | Option<[StrategyType](vault.md#strategytype)>                          | The strategy to deposit the funds to. If not specified, the funds will go to the vault. |

#### DepositMsg

The struct that should be encoded to base64 and attached as the message to specify the deposit options.

```rust
pub struct DepositMsg {
    pub strategy: Option<StrategyType>,
    pub amount: Option<Coin>,
    pub deposit_msg: Option<Binary>,
}
```

| Name          | Type                                                           | Description                                                                                                                  |
| ------------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `strategy`    | Option<[StrategyType](vault.md#strategytype)>                  | Optional strategy to deposit the funds to. If not specified then the funds are deposited into the vault.                     |
| `amount`      | Option<[Coin](../platform-and-framework/common-types.md#coin)> | Optional amount to deposit from the sent funds. If not specified then all the sent funds are deposited.                      |
| `deposit_msg` | Option\<Binary>                                                | Optional message to attach  along with the deposit.This is used to execute a message on the strategy receiving the deposit.  |

#### StrategyType

```rust
pub enum StrategyType {
}
```

{% hint style="warning" %}
No strategy is currently supported.
{% endhint %}

### WithdrawVault

Withdraw funds from the vault or one of the strategies.

{% hint style="warning" %}
The `recipient` field is not used if withdrawing from a strategy.

An address can only withdraw from deposits made to their address.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
    WithdrawVault {
        recipient: Option<Recipient>,
        withdrawals: Vec<Withdrawal>,
        strategy: Option<StrategyType>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"withdraw_vault":{
"recipient":{
    "address":"andr1..."
    },
"withdrawals":[
                {
                    "token":"uandr",
                    "withdrawal_type":{
                        "percentage":"0.3"
                        }
                    },    
                ...
          ]          
    }
 }
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                                     |                                                                                                                                      |
| ------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| `recipient`   | Option<[Recipient](../platform-and-framework/common-types.md#recipient)> | The address to receive the withdrawn funds when withdrawing from vault. Defaults to the sender. Not used if a strategy is specified. |
| `withdrawals` | Vec<[Withdrawal](vault.md#withdrawal)>                                   | The funds to withdraw.                                                                                                               |
| `strategy`    | Option<[StrategyType](vault.md#strategytype)>                            | The strategy to withdraw from. If not specified, the funds are withdrawn from the vault.                                             |

#### Withdrawal

```rust
pub struct Withdrawal {
    pub token: String,
    pub withdrawal_type: Option<WithdrawalType>,
}
```

| Name              | Type                    | Description             |
| ----------------- | ----------------------- | ----------------------- |
| `token`           | String                  | The token to withdraw.  |
| `withdrawal_type` | Option\<WithdrawalType> | The type of withdrawal. |

#### WithdrawalType

```rust
pub enum WithdrawalType {
    Amount(Uint128),
    Percentage(Decimal),
}
```

There are two main withdrawal types:

* **Amount:** Withdraw a flat amount from the vault/strategy.
* **Percentage:** Withdraw a percentage of funds found in the vault/strategy.

### UpdateStrategy

Updates the contract address used for the specified strategy.

{% hint style="warning" %}
The Vault needs to be an operator of the strategy contract.

Only availabe to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
  UpdateStrategy {
        strategy: StrategyType,
        address: AndrAddr,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_strategy":{
    "strategy":"some-strategy",
    "address":"andr1..."    
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                           | Description                                       |
| ---------- | -------------------------------------------------------------- | ------------------------------------------------- |
| `strategy` | [StrategyType](vault.md#strategytype)                          | The strategy to set a new contract address for.   |
| `address`  | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | Reference to the ADO address of the new strategy. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### VaultBalance

Gets the balance for the specified address. Can be either the their balance in the vault or in one of the implemented strategies.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Binary)]
    VaultBalance {
        address: String,
        strategy: Option<StrategyType>,
        denom: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"balance":{
     "address":"andr1...",
     "denom":"uandr"
     }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                           | Description                                                                               |
| ---------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `address`  | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The address to get the balance of.                                                        |
| `strategy` | Option<[StrategyType](vault.md#strategytype)>                  | The strategy to get the balance of. If not specified, the funds in the vault are queried. |
| `denom`    | Option\<String>                                                | Optional denom for the funds.                                                             |

Returns the balance response corresponding to the strategy. If the strategy is not specified, it would return a `Vec<Coin>` containing the balance of the `address`.

### StrategyAddress

Returns the contract address of the specified strategy.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
    #[returns(Binary)]
    StrategyAddress {
        strategy: StrategyType,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"strategy_address":{
           "strategy":"anchor"
           
       }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                  | Description                         |
| ---------- | ------------------------------------- | ----------------------------------- |
| `Strategy` | [StrategyType](vault.md#strategytype) | The strategy to get the address of. |

#### StrategyAddressResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct StrategyAddressResponse {
    pub strategy: StrategyType,
    pub address: String,
}
```
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
"strategy":"anchor",
<strong>    "address":"andr1..."
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

| Name       | Type                                  | Description                           |
| ---------- | ------------------------------------- | ------------------------------------- |
| `strategy` | [StrategyType](vault.md#strategytype) | The strategy we want the address for. |
| `address`  | String                                | The address of the `strategy`.        |

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
