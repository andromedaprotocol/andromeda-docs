# Anchor Earn

## Introduction

The Anchor ADO is a smart contract that allows users to deposit funds to utilize the functionalities used by anchor protocol. Anchor Earn allows depositing funds into Anchor and earning an APY.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub primitive_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"primitive_contract":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                                                       |
| -------------------- | ------ | --------------------------------------------------------------------------------- |
| `primitive_contract` | String | The primitive contract that would contain all the anchor addresses needed for the |

The primitive contract will have all the anchor addresses needed  before working with the anchor contract. These include the contract addresses for:

```rust
pub const ANCHOR_MARKET: &str = "anchor_market_contract";
pub const ANCHOR_AUST: &str = "anchor_aust_contract";
```

| Name            | Description                                                                                                                                                                                     |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ANCHOR_MARKET` | Information on the market contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/money-market/market).                                                                    |
| `ANCHOR_AUST`   | Cw20 compliant contract. Contract address of the deployed  anchor AUST contract can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts) (money market section). |

The contract addresses of these deployed contracts by anchor can be found [here](https://docs.anchorprotocol.com/smart-contracts/deployed-contracts).

## ExecuteMsg

### AndrReceive

Deposits stable assets to get interest or withdraw funds from position. Depends on the attached message.

```rust
pub enum ExecuteMsg {
     AndrReceive(AndromedaMsg),
    }
```

#### AndromedaMsg

There are many andromeda messages but for Anchor, we will focus on `Receive` and `Withdraw`.

```rust
pub enum AndromedaMsg {
    Receive(Option<Binary>),
    Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    },
```

For the rest of the `AndromedaMsg` check [`AndrReceive`](../ado\_base/andrreceive-andrquery.md#andromedamsg).

### Receive

Deposits funds into an anchor position for the [`recipient`](../common-types/recipient.md) if specified and the sender if not.

{% hint style="warning" %}
Exactly 1 type of native coin should be deposited.

For now only "uusd" is accepted.
{% endhint %}

```rust
fn execute_andr_receive(
    deps: DepsMut,
    env: Env,
    info: MessageInfo,
    msg: AndromedaMsg,
) -> Result<Response, ContractError> {
    match msg {
        AndromedaMsg::Receive(data) => match data {
            None => execute_deposit(deps, env, info, None),
            Some(_) => {
                let recipient: Recipient = parse_message(&data)?;
                execute_deposit(deps, env, info, Some(recipient))
            }
        },
    }
```

The receive message can be empty or contain a recipient to get the position.

{% tabs %}
{% tab title="JSON" %}
```json
{
"andr_receive": {
    "receive":{}
    }
    
  or
  
  {
"andr_receive": {
    "receive":{
        "recipient":{
            "addr":"terra1... 
        }
    }
 }
```
{% endtab %}
{% endtabs %}

### Withdraw

Withdraws funds from the position of the specified `recipient`.

{% hint style="warning" %}
Only the owner of the position or owner/operators of the contract can execute withdraw.

Recipient needs to be of type `Addr`.

tokens\_to\_withdraw should contain only 1 `Withdrawal` and is not optional in this case.

Only "uusd" or "aust" can be withdrawn.
{% endhint %}

```rust
 pub enum AndromedaMsg {
 Withdraw {
        recipient: Option<Recipient>,
        tokens_to_withdraw: Option<Vec<Withdrawal>>,
    },
  }
```

| Name                 | Type                                              | Description                                                              |
| -------------------- | ------------------------------------------------- | ------------------------------------------------------------------------ |
| `recipient`          | Option<[Recipient](../common-types/recipient.md)> | The address to withdraw funds from. Defaults to sender if not specified. |
| `tokens_to_withdraw` | Option\<Withdrawal>                               | The tokens to withdraw.                                                  |

#### Withdrawal

{% hint style="warning" %}
If `withdrawal_type` is not specified then the total balance is withdrawn.
{% endhint %}

```rust
pub struct Withdrawal {
    pub token: String,
    pub withdrawal_type: Option<WithdrawalType>,
}
```

#### WithdrawalType

Can be a percentage of the total or a fixed specified amount.

```rust
pub enum WithdrawalType {
    Amount(Uint128),
    Percentage(Decimal),
}
```

#### JSON

```json
{
"andr_receive": {
    "withdraw":{
        "recipient":{
            "addr":"terra1..."
            },
    "tokens_to_withdraw":[
      {        
         "token":"uusd",
         "withdrawal_type":{
             "percentage":"0.3"
             }
         }
     ]
 }
        
```

## QueryMsg

### AndrQuery

```rust
fn handle_andromeda_query(
    deps: Deps,
    env: Env,
    msg: AndromedaQuery,
) -> Result<Binary, ContractError> {
    match msg {
        AndromedaQuery::Get(data) => {
            let recipient: String = parse_message(&data)?;
            encode_binary(&query_position(deps, recipient)?)
        }
        _ => ADOContract::default().query(deps, env, msg, query),
    }
}
```

If the type is a `Get`, it will query the current anchor position and return a `PositionResponse`:

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PositionResponse {
    pub recipient: Recipient,
    pub aust_amount: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recipient":{
  "addr":"terra1...",
  }
"aust_amount":"10000"
}
```
{% endtab %}
{% endtabs %}

The rest of the executes can be found in the [`AndrQuery`](../ado\_base/andrreceive-andrquery.md#andrquery) section.
