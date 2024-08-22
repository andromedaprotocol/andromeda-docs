# CW721 Timelock

## Introduction

The **CW721 Timelock ADO** allows users to lock an NFT for a certain period of time (Between one day and one year). Once the lock has expired, anyone can call the `claim` function which will send the NFT to the defined recipient (Defined before the lock period starts).&#x20;

**Ado\_type:** cw721-timelock

## InstantiateMsg       &#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub authorized_token_addresses: Option<Vec<AndrAddr>>,
    pub kernel_address: String,
    pub owner: Option<String>
}       
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"authorized_token_address":["andr1...","andr1..."],
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name                         | Type                                                                                                               | Description                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `authorized_token_addresses` | Option\<Vec<[AndrAddr](https://docs.andromedaprotocol.io/andromeda/platform-and-framework/common-types#andraddr)>> | Optional set of CW721 contract addresses to be allowed to send NFTs to the Timelock ADO. If not specified, then any NFT can be sent to be locked. |
| `kernel_address`             | String                                                                                                             | Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts.             |
| `owner`                      | Option\<String>                                                                                                    | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                       |

## ExecuteMsg

### ReceiveNft

Receives a token from a `SendNft` (Called from a CW721 ADO) and locks it based on the parameters defined in the `TimelockNft` hook message.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  ReceiveNft(Cw721ReceiveMsg),
  }
```
{% endtab %}
{% endtabs %}

#### Cw721ReceiveMsg

```rust
pub struct Cw721ReceiveMsg {
    pub sender: String,
    pub token_id: String,
    pub msg: Binary,
}
```

The `msg` field in the `Cw721ReceiveMsg` struct should be a `Cw721HookMsg` of type `TimelockNft`.

#### Cw721HookMsg

{% hint style="warning" %}
The minimum `lock_time` that can be set is 86\_400 which is one day.

The maximum `lock_time` that can be set is 31\_536\_000 which is one year.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Cw721HookMsg {
    TimelockNft {
        lock_duration: MillisecondsDuration,
        recipient: Recipient,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"timelock_nft":{
    "lock_duration": 400000,
    "recipient":{
       "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type                 | Description                                                                                     |
| --------------- | -------------------- | ----------------------------------------------------------------------------------------------- |
| `lock_duration` | MillisecondsDuration | The amount of time to lock the NFT for. Specified in milliseconds.                              |
| `recipient`     | Option\<String>      | The address to receive the NFT after the lock is done. Defaults to the sender if not specified. |

### ClaimNft

{% hint style="info" %}
Anyone is able to call this execute message.&#x20;
{% endhint %}

Transfers the given token to the recipient (Defined when the lock was set) once the time lock has expired.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  ClaimNft {
        cw721_contract: AndrAddr,
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_nft":{
    "cw721_contract":"andr1...",
    "token_id":"3"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                                                          |
| ---------------- | ------ | -------------------------------------------------------------------- |
| `cw721_contract` | String | The contract address of the CW721 ADO that sent the NFT to this ADO. |
| `token_id`       | String | The token Id of the locked NFT.                                      |

### Base Executes

The rest of the execute messages can be found in the ADO Base section.

## QueryMsg

### UnlockTime

Queries the a timestamp in milliseconds for when the NFT lock time ends.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(UnlockTimeResponse)]
     UnlockTime {
        cw721_contract: AndrAddr,
        token_id: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unlock_time":{
    "cw721_contract":"andr1...",
    "token_id":"3"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                                                          |
| ---------------- | ------ | -------------------------------------------------------------------- |
| `cw721_contract` | String | The contract address of the CW721 ADO that sent the NFT to this ADO. |
| `token_id`       | String | The token Id of the  NFT we are checking the lock for.               |

#### UnlockTimeResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct UnlockTimeResponse {
    pub unlock_time: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unlock_time": 1724053243374
}
```
{% endtab %}
{% endtabs %}

| Name          | Type | Description                                                                                                      |
| ------------- | ---- | ---------------------------------------------------------------------------------------------------------------- |
| `unlock_time` | u64  | A [timestamp](https://www.unixtimestamp.com) in milliseconds specifying when the lock ends on the specified NFT. |

### NftDetails

Queries the unlock time and the designated recipient of the NFT when the NFT is unlocked.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg {
 #[returns(NftDetailsResponse)]
 NftDetails {
        cw721_contract: AndrAddr,
        token_id: String,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"nft_details":{
    "cw721_contract":"andr1...",
    "token_id":"2"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                                                          |
| ---------------- | ------ | -------------------------------------------------------------------- |
| `cw721_contract` | String | The contract address of the CW721 ADO that sent the NFT to this ADO. |
| `token_id`       | String | The token Id of the  NFT we are checking the lock for.               |

#### NftDetailsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct NftDetailsResponse {
    pub unlock_time: u64,
    pub recipient: Addr,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unlock_time":1724053243374
"recipient":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name          | Type | Description                                                                                                      |
| ------------- | ---- | ---------------------------------------------------------------------------------------------------------------- |
| `unlock_time` | u64  | A [timestamp](https://www.unixtimestamp.com) in milliseconds specifying when the lock ends on the specified NFT. |
| `recipient`   | Addr | The address to recieve the NFT when it is claimed after the lock period is over.                                 |

### IsLocked

Checks if the specified NFT is locked.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
     #[returns(IsLockedResponse)]
     IsLocked {
        cw721_contract: AndrAddr,
        token_id: String,
     }
   }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"is_locked":{
    "cw721_contract":"andr1...",
    "token_id":"token-7"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                                                          |
| ---------------- | ------ | -------------------------------------------------------------------- |
| `cw721_contract` | String | The contract address of the CW721 ADO that sent the NFT to this ADO. |
| `token_id`       | String | The token Id of the  NFT we are checking the lock for.               |

#### IsLockedResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct IsLockedResponse {
    pub is_locked: bool,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"is_locked": true
}
```
{% endtab %}
{% endtabs %}

Returns true if the NFT is locked and false otherwise.

### Base Queries

The rest of the query messages can be found in the ADO Base section.
