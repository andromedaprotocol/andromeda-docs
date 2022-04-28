# Mirror

## Introduction

The Mirror ADO allows users to use Mirror Protocol functionality in an ADO. If not familiar with mirror and what they offer refer to their docs found [here](https://docs.mirror.finance).

The following mirror contracts are supported by our ADO:

* Mint
* Staking
* Gov&#x20;
* Lock

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub primitive_contract: String,
    pub operators: Option<Vec<String>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"primitive_contract":"terra1...",
"operators":["terra1...","terra1...",...]
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                  | Description                                                     |
| -------------------- | --------------------- | --------------------------------------------------------------- |
| `primitive_contract` | String                | The primitive contract to store the different mirror contracts. |
| `operators`          | Option\<Vec\<String>> | Optional vector of operators for the contract.                  |

The primitive contract will have all the mirror addresses needed before working with the contract. These include the contract addresses for:

```rust
pub const MIRROR_MINT: &str = "mirror_mint_contract";
pub const MIRROR_STAKING: &str = "mirror_staking_contract";
pub const MIRROR_GOV: &str = "mirror_gov_contract";
pub const MIRROR_LOCK: &str = "mirror_lock_contract";
pub const MIRROR_MIR: &str = "mirror_mir_contract";
```

| Name            | Description                                                                                             |
| --------------- | ------------------------------------------------------------------------------------------------------- |
| MIRROR\_MINT    | Information on the mint contract can be found [here](https://docs.mirror.finance/contracts/mint).       |
| MIRROR\_STAKING | Information on the staking contract can be found [here](https://docs.mirror.finance/contracts/staking). |
| MIRROR\_GOV     | Information on the gov contract can be found [here](https://docs.mirror.finance/contracts/gov).         |
| MIRROR\_LOCK    | Information on the lock contract can be found [here](https://docs.mirror.finance/contracts/lock).       |
| MIRROR\_MIR     | The mirror token contract address.                                                                      |

## ExecuteMsg

### Receive

Send Cw-20 tokens to this contract with an attached msg.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg { 
   Receive(Cw20ReceiveMsg),
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

The `msg` in `Cw20ReceiveMsg` should be `Cw20HookMsg`.

```rust
pub enum Cw20HookMsg {
    MirrorMintCw20HookMsg(MirrorMintCw20HookMsg),
    MirrorStakingCw20HookMsg(MirrorStakingCw20HookMsg),
    MirrorGovCw20HookMsg(MirrorGovCw20HookMsg),
}
```

* `MirrorMintCw20HookMsg`

```rust
pub enum Cw20HookMsg {
    OpenPosition {
        asset_info: AssetInfo,
        collateral_ratio: Decimal,
        short_params: Option<ShortParams>,
    },
    Deposit { position_idx: Uint128 },
    Burn { position_idx: Uint128 },
    Auction { position_idx: Uint128 },
}
```

Documentation on the Mint hooks can be found [here](https://docs.mirror.finance/contracts/mint#receive-hooks).

* `MirrorStakingCw20HookMsg`

```rust
pub enum Cw20HookMsg {
    Bond { asset_token: String },
    DepositReward { rewards: Vec<(String, Uint128)> },
}
```

Documentation on the Staking hooks can be found [here](https://docs.mirror.finance/contracts/staking#receive-hooks).

* `MirrorGovCw20HookMsg`

```rust
pub enum Cw20HookMsg {
    StakeVotingTokens {},
    CreatePoll {
        title: String,
        description: String,
        link: Option<String>,
        execute_msg: Option<PollExecuteMsg>,
    },
    DepositReward {},
}
```

Documentation for the Gov hooks can be found [here](https://docs.mirror.finance/contracts/gov#receive-hooks).

### MirrorMintExecuteMsg

The Mint contract Execute messages.

```rust
pub enum ExecuteMsg {
      MirrorMintExecuteMsg(MirrorMintExecuteMsg),
      }
```

#### MirrorMintExecuteMsg

```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),
    
    UpdateConfig {
        owner: Option<String>,
        oracle: Option<String>,
        collector: Option<String>,
        collateral_oracle: Option<String>,
        terraswap_factory: Option<String>,
        lock: Option<String>,
        token_code_id: Option<u64>,
        protocol_fee_rate: Option<Decimal>,
        staking: Option<String>,
    },
 
    UpdateAsset {
        asset_token: String,
        auction_discount: Option<Decimal>,
        min_collateral_ratio: Option<Decimal>,
        ipo_params: Option<IPOParams>,
    },
 
    RegisterAsset {
        asset_token: String,
        auction_discount: Decimal,
        min_collateral_ratio: Decimal,
        ipo_params: Option<IPOParams>,
    },
    RegisterMigration {
        asset_token: String,
        end_price: Decimal,
    },

    TriggerIPO {
        asset_token: String,
    },
    
    OpenPosition {
        collateral: Asset,
        asset_info: AssetInfo,
        collateral_ratio: Decimal,
        short_params: Option<ShortParams>,
    },
    
    Deposit {
        position_idx: Uint128,
        collateral: Asset,
    },
    
    Withdraw {
        position_idx: Uint128,
        collateral: Option<Asset>,
    },
    
    Mint {
        position_idx: Uint128,
        asset: Asset,
        short_params: Option<ShortParams>,
    },
}
```

Documentation for the different messages can be found [here](https://docs.mirror.finance/contracts/mint#handlemsg).

### MirrorStakingExecuteMsg

The staking contract execute messages.

```rust
 pub enum ExecuteMsg {
  MirrorStakingExecuteMsg(MirrorStakingExecuteMsg),
  }
```

#### MirrorStakingExecuteMsg

```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),

    UpdateConfig {
        owner: Option<String>,
        premium_min_update_interval: Option<u64>,
        short_reward_contract: Option<String>,
    },
    RegisterAsset {
        asset_token: String,
        staking_token: String,
    },

    Unbond {
        asset_token: String,
        amount: Uint128,
    },
 
    Withdraw {

    },
 
    AutoStake {
        assets: [Asset; 2],
        slippage_tolerance: Option<Decimal>,
    },
  
    AutoStakeHook {
        asset_token: String,
        staking_token: String,
        staker_addr: String,
        prev_staking_token_amount: Uint128,
    },

 
    AdjustPremium {
        asset_tokens: Vec<String>,
    },


    IncreaseShortToken {
        asset_token: String,
        staker_addr: String,
        amount: Uint128,
    },
    DecreaseShortToken {
        asset_token: String,
        staker_addr: String,
        amount: Uint128,
    },
}
```

Documentation for the different messages can be found [here](https://docs.mirror.finance/contracts/staking#handlemsg).

### MirrorGovExecuteMsg

The Gov contract execute messages.

```rust
pub enum ExecuteMsg {
    MirrorGovExecuteMsg(MirrorGovExecuteMsg),
    }
```

#### MirrorGovExecuteMsg

```rust
pub enum ExecuteMsg {
    Receive(Cw20ReceiveMsg),
    UpdateConfig {
        owner: Option<String>,
        quorum: Option<Decimal>,
        threshold: Option<Decimal>,
        voting_period: Option<u64>,
        effective_delay: Option<u64>,
        proposal_deposit: Option<Uint128>,
        voter_weight: Option<Decimal>,
        snapshot_period: Option<u64>,
    },
    CastVote {
        poll_id: u64,
        vote: VoteOption,
        amount: Uint128,
    },
    WithdrawVotingTokens {
        amount: Option<Uint128>,
    },
    WithdrawVotingRewards {
        poll_id: Option<u64>,
    },
    StakeVotingRewards {
        poll_id: Option<u64>,
    },
    EndPoll {
        poll_id: u64,
    },
    ExecutePoll {
        poll_id: u64,
    },
    SnapshotPoll {
        poll_id: u64,
    },
}
```

Documentation for the different messages can be found [here](https://docs.mirror.finance/contracts/gov#executemsg).

### MirrorLockExecuteMsg

The execute messages for the lock contract

```rust
pub enum ExecuteMsg {
    MirrorLockExecuteMsg(MirrorLockExecuteMsg),
    }
```

#### MirrorLockExecuteMsg

```rust
pub enum ExecuteMsg {
    UpdateConfig {
        owner: Option<String>,
        mint_contract: Option<String>,
        base_denom: Option<String>,
        lockup_period: Option<u64>,
    },
    LockPositionFundsHook {
        position_idx: Uint128,
        receiver: String,
    },
    UnlockPositionFunds {
        positions_idx: Vec<Uint128>,
    },
    ReleasePositionFunds {
        position_idx: Uint128,
    },
}
```

Documentation for the different messages can be found [here](https://docs.mirror.finance/contracts/lock#handlemsg).

### AndrReceive

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md#andrrecieve).

## QueryMsg

### AndrQuery

Check [AndrQuery](../ado\_base/andrreceive-andrquery.md#andrquery).
