---
description: The messages to interact with the Vault ADO.
---

# Vault

## Introduction

The purpose of the vault ADO is to provide central funds for a mission. This means, it will allow the mission to deposit funds to the vault, and these funds can be proxied to various yield strategies.

{% hint style="warning" %}
The only yield strategy **currently** available is anchor.
{% endhint %}

Funds can be deposited to the vault and a record of the deposited funds is stored. Strategies can be added by the contract owner at any point by calling `UpdateStrategy`.

Deposits can be made to yield strategies in partial fashion, sending part of what you would like to deposit and taking the rest from what the vault is currently holding. For example, a wallet could have 10 UST held within the vault, they wish to deposit 50 UST to Anchor, in this case they can send 40 UST with a deposit message and 10 UST will be removed from their deposits and sent to the according Anchor contract.

Withdrawals are done in similar fashion using our `Withdrawal` struct. Withdrawals can be made from the vault itself or from its according yield strategies.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub operators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"operators":["terra1...","terra1...",...]
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                  | Description                                                        |
| ----------- | --------------------- | ------------------------------------------------------------------ |
| `operators` | Option\<Vec\<String>> | An optional list of addresses to act as operators on the contract. |

### ExecuteMsg

### AndrReceive

Deposits funds to the vault/strategy by sending them to the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
  { 
    AndrReceive(AndromedaMsg),
  }
}
```
{% endtab %}
{% endtabs %}

The [`AndromedaMsg`](../ado\_base/andrreceive-andrquery.md#andromedamsg)  needs to be of type `receive` to deposit funds to the vault. It will then execute a [Deposit](vault.md#deposit) with the `recipient` being the sender, the `amount` is the amount of funds sent. The funds are deposited to the central vault.

If the `AndromedaMsg` is not of type receive, then it is used to execute one of the default [AndrReceive messages](../ado\_base/andrreceive-andrquery.md#andrrecieve).

### Deposit

Allocates funds from the vault to the specified strategy for the specified recipient. If the recipient is not specified, then it will default to the sender.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Deposit {
        recipient: Option<Recipient>,
        amount: Option<Coin>,
        strategy: Option<StrategyType>,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"deposit":{
"recipient":
   {
   "addr":"terra1...",
   },
   
"strategy":"anchor"

    }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type                                              | Description                                                                             |
| ----------- | ------------------------------------------------- | --------------------------------------------------------------------------------------- |
| `recipient` | Option<[Recipient](../common-types/recipient.md)> | The recipient of the deposit. Defaults to the sender if not specified.                  |
| `amount`    | Option<[Coin](../common-types/coin.md)>           | The amount to deposit. If not specified then the sent funds are used as the amount.     |
| `strategy`  | Option<[StrategyType](vault.md#strategytype)>     | The strategy to deposit the funds to. If not specified, the funds will go to the vault. |

#### StrategyType

```rust
pub enum StrategyType {
    Anchor,
}
```

### Withdraw

Withdraw funds from the vault or one of the strategies.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
    Withdraw {
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
"withdraw":{
"recipient":{
"addr":"terra1..."
    }
"withdrawals":[
     {
     "token":"UST",
     "withdrawal_type":{
      "percentage":"40"
     },
     ...
     ]
    }
 }
```
{% endtab %}
{% endtabs %}

| Name          | Type                                              |                                                                                          |
| ------------- | ------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `recipient`   | Option<[Recipient](../common-types/recipient.md)> | The address to receive the withdrawn funds.                                              |
| `withdrawals` | Vec\<Withdrawal>                                  | The funds to withdraw.                                                                   |
| `strategy`    | Option<[StrategyType](vault.md#strategytype)>     | The strategy to withdraw from. If not specified, the funds are withdrawn from the vault. |

#### Withdrawal

```rust
pub struct Withdrawal {
    pub token: String,
    pub withdrawal_type: Option<WithdrawalType>,
}
```

| Name              | Type                    | Description                                                                 |
| ----------------- | ----------------------- | --------------------------------------------------------------------------- |
| `token`           | String                  | The denom of the funds to withdraw.                                         |
| `withdrawal_type` | Option\<WithdrawalType> | The type of withdrawal. Can vary between a specific amount or a percentage. |

#### WithdrawalType

Withdrawal can be a certain amount or a percentage of the tokens.

```rust
pub enum WithdrawalType {
    Amount(Uint128),
    Percentage(Uint128),
}
```

The rest of the executes can be found in the [`AndrReceive`](../ado\_base/andrreceive-andrquery.md#andrrecieve) section.

### UpdateStrategy

Updates the contract address used for the specified strategy.

{% tabs %}
{% tab title="Rust" %}
```rust
  UpdateStrategy {
        strategy: StrategyType,
        address: AndrAddress,
    },
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_strategy":{
    "strategy":"anchor",
    "address":{
        "identifier":"terra1..."
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                    | Description                                     |
| ---------- | ------------------------------------------------------- | ----------------------------------------------- |
| `strategy` | [StrategyType](vault.md#strategytype-1)                 | The strategy to set a new contract address for. |
| `address`  | [AndrAddress](../common-types/recipient.md#andraddress) | The new contract address                        |

## QueryMsg

### Balance

Gets the balance for the specified address. Can be either the their balance in the vault or in one of the implemented strategies.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    Balance {
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
"address":"terra1...",
"denom":"UST"
     }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                             | Description                                                                               |
| ---------- | ------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| `address`  | String                                           | The address to get the balance of.                                                        |
| `strategy` | Option<[StrategyType](vault.md#strategyaddress)> | The strategy to get the balance of. If not specified, the funds in the vault are queried. |
| `denom`    | Option\<String>                                  | Optional denom for the funds.                                                             |

Returns the balance response corresponding to the strategy. If the strategy is not specified, it would return a `Vec<Coin>` containing the balance of the `address`.

### StrategyAddress

Returns the contract address of the specified strategy.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
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
           "strategy":"Anchor"
           
       }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                  | Description                         |
| ---------- | ------------------------------------- | ----------------------------------- |
| `Strategy` | [StrategyType](vault.md#strategytype) | The strategy to get the address of. |

#### StrategyAddresResponse

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
```json
{
"strategy":{
            "Anchor"
            },
"address":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                  | Description                           |
| ---------- | ------------------------------------- | ------------------------------------- |
| `strategy` | [StrategyType](vault.md#strategytype) | The strategy we want the address for. |
| `address`  | String                                | The address of the `strategy`.        |

### AndrQuery

```rust
pub enum QueryMsg {
    AndrQuery(AndromedaQuery),
    }
```

If the [`AndromedaQuery`](../ado\_base/andrreceive-andrquery.md#andromedaquery) is of type `Get` , the contract will query the balance of the specified address(data).&#x20;

```rust
fn handle_andromeda_query(
    deps: Deps,
    env: Env,
    msg: AndromedaQuery,
) -> Result<Binary, ContractError> {
    match msg {
        AndromedaQuery::Get(data) => {
            let address: String = parse_message(&data)?;
            encode_binary(&query_balance(deps, env, address, None, None)?)
        }
        _ => ADOContract::default().query(deps, env, msg, query),
    }
}
```

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andromedaquery) for the rest of the default queries.