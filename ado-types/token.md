---
description: The message definitions for the Andromeda token contract.
---

# Token

### InstantiateMarketingInfo

```rust
pub struct InstantiateMarketingInfo {
    pub project: Option<String>,
    pub description: Option<String>,
    pub marketing: Option<String>,
    pub logo: Option<Logo>,
}
```

| Name          | Type            |                                                                                    |
| ------------- | --------------- | ---------------------------------------------------------------------------------- |
| `project`     | Option\<String> | A URL pointing to the project behind this token.                                   |
| `description` | Option\<String> | A longer description of the token and it's utility. Designed for tooltips or such. |
| `marketing`   | Option\<String> | The address (if any) who can update this data structure.                           |
| `logo`        | Option\<Logo>   | A link to the logo, or a comment that there is an on-chain logo stored.            |

### MinterResponse

```rust
pub struct MinterResponse {
    pub minter: String,
    pub cap: Option<Uint128>,
}
```

| Name     | Type             | Description                                                 |
| -------- | ---------------- | ----------------------------------------------------------- |
| `minter` | String           | The address to assign as a minter.                          |
| `cap`    | Option\<Uint128> | A hard cap on total supply that can be achieved by minting. |

{% hint style="info" %}
The cap refers to the total supply. If None, there is unlimited cap.
{% endhint %}

### Module

A struct describing a token module, provided with the instantiation message this struct uses to record the info about the module and how/if it should be instantiated.

```rust
pub struct Module {
    pub module_type: ModuleType,
    pub instantiate: InstantiateType,
    pub is_mutable: bool,

```

#### ModuleType

An enum describing the different available modules for any Andromeda Token contract.

```rust
pub enum ModuleType {
    Rates,
    Offers,
    AddressList,
    Auction,
    Receipt,
    Other,
}
```

#### InstantiateType

Modules can be instantiated in two different ways:

* New: Provide an instantiation message for the contract, a new contract will be instantiated and the address recorded
* &#x20;Address: Provide an address for an already instantiated module contract

```rust
pub enum InstantiateType {
    New(Binary),
    Address(String),
}
```

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub decimals: u8,
    pub initial_balances: Vec<Cw20Coin>,
    pub mint: Option<MinterResponse>,
    pub marketing: Option<InstantiateMarketingInfo>,
    pub modules: Option<Vec<Module>>,
    pub primitive_contract: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "name": "mytoken",
 "symbol":"MYT",
 "decimals": 6,
 "initial_balances":[
                {
              "address":"terra1...",
              "amount":"500",
          }
              ...
        ],
 "mint":{
       "minter":"terra1...",
       "cap": "1000000",
      },
  "primitive_contract":"terra1...",
 }  
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                                  | Description                                                                                                                          |
| -------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `name`               | String                                                                | The name of  the token.                                                                                                              |
| `symbol`             | String                                                                | The symbol of the token.                                                                                                             |
| `decimals`           | u8                                                                    | The number of decimals for the token.                                                                                                |
| `initial_balances`   | Vec\<Cw20Coin>                                                        | A vector containing a list of addresses and the amount of coin to initialize each.                                                   |
| `mint`               | Option<[MinterResponse](token.md#undefined)>                          | Optional field to define a minter for the token and an optional  cap for the total supply of tokens that can be minted.              |
| `marketing`          | Option<[InstantiateMarketingInfo](token.md#instantiatemarketinginfo)> | Optional field to define the marketing information of the project.                                                                   |
| `modules`            | Option\<Vec\<Module>>                                                 | Optional vector to define any modules to attach to the contract. Each module has an index that can be referenced by other functions. |
| `primitive_contract` | String                                                                | The primitive contract used to The primitive contract address used to retrieve contract addresses.                                   |

## ExecuteMsg

### Mint

Only with the "mint" extension. If authorized, creates amount new tokens and adds to the recipient balance.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
   Mint {
       recipient: String,
       amount: Uint128
        }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{ 
 "mint":{
    "recipient":"terra1...",
    "amount": "100"
    }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type    | Description                               |
| ----------- | ------- | ----------------------------------------- |
| `recipient` | String  | The address to receive the minted tokens. |
| `amount`    | Uint128 | The amount of tokens to mint.             |

### Transfer

Transfer is a base message to move tokens to another account without triggering actions.

{% hint style="info" %}
The `amount` sent might be affected depending on the attached modules.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg { 
  Transfer { 
     recipient: String,
     amount: Uint128 
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "transfer":{
      "recipient":"terra1...",
      "amount": "100",
      }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type    | Description                            |
| ----------- | ------- | -------------------------------------- |
| `recipient` | String  | The address to transfer the tokens to. |
| `amount`    | Uint128 | The amount of tokens to transfer.      |

### Send

Send is a base message to transfer tokens to a contract and trigger an action on the receiving contract.

{% hint style="info" %}
The `amount` sent might be affected depending on the attached modules.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
     Send {
        contract: String,
        amount: Uint128,
        msg: Binary,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "send":{
    "contract":"terra1...",
    "amount":"100",
    "msg": null
    }
 }
```
{% endtab %}
{% endtabs %}

| Name       | Type    | Description                                     |
| ---------- | ------- | ----------------------------------------------- |
| `contract` | String  | The address of the receiving contract.          |
| `amount`   | Uint128 | The amount to send.                             |
| `msg`      | Binary  | A message to be sent to the receiving contract. |

### Burn

Burn is a base message to destroy tokens forever

{% tabs %}
{% tab title="Rust" %}
```rust
   pub enum ExecuteMsg {
      Burn {
        amount: Uint128,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "burn": {
     "amount": "100"
     }
 }
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                      |
| -------- | ------- | -------------------------------- |
| `amount` | Uint128 | The amount of coins to be burnt. |

### IncreaseAllowance

Sets an `amount` of tokens from the owner that the specified `spender` can interact with.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
 IncreaseAllowance {
        spender: String,
        amount: Uint128,
        expires: Option<Expiration>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "increase_allowance":{
     "spender":"terra1...",
     "amount":"1000",
     }
 }
     
```
{% endtab %}
{% endtabs %}

| Name      | Type                | Description                                            |
| --------- | ------------------- | ------------------------------------------------------ |
| `spender` | String              | The address to receive the allowance.                  |
| `amount`  | Uint128             | The amount of tokens to give the spender access to.    |
| `expires` | Option\<Expiration> | Optional Expiration for the allowance of the spender.  |

### DecreaseAllowance

Decreases the allowance set for the `spender` by the set `amount.`&#x20;

{% hint style="info" %}
The amount specified in `DecreaseAllowance` does not replace the old amount but is subtracted from it. If the result is 0 or less, then the spender no longer has an Allowance from the owner.

If an `Expiration` is set, it will overwrite previously set Expiration
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg {
  DecreaseAllowance {
        spender: String,
        amount: Uint128,
        expires: Option<Expiration>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "decrease_allowance": {
    "spender":"terra1...",
    "amount":"100",
    "expires": {
      "at_height": "500"
      }
   }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                | Description                                           |
| --------- | ------------------- | ----------------------------------------------------- |
| `spender` | String              | The address to have their allowance decreased.        |
| `amount`  | Uint128             | The amount to decrease the allowance by.              |
| `expires` | Option\<Expiration> | Optional expiration for the allowance of the spender. |

### TransferFrom

Transfers the `amount` of tokens from the `owner` address to the `recipient.`

{% hint style="info" %}
The `amount specified` cannot exceed the allowance of the address executing `TransferFrom.`&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   TransferFrom {
        owner: String,
        recipient: String,
        amount: Uint128,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "transfer_from":{
   "owner":"terra1...",
   "recipient":"terra1...",
   "amount":"50",
     }
 }
 
```
{% endtab %}
{% endtabs %}

| Name        | Type    | Description                                                   |
| ----------- | ------- | ------------------------------------------------------------- |
| `owner`     | String  | The owner address that has the tokens to transfer.            |
| `recipient` | String  | The address the receive the tokens.                           |
| `amount`    | Uint128 | The amount of tokens to send from the owner to the recipient. |

### SendFrom

Sends the  `amount` of tokens from the `owner` address to the `contract` address. Can use a msg to trigger an action on the receiving contract.

{% hint style="info" %}
The `amount specified` cannot exceed the allowance of the address executing `TransferFrom.`&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg{
    SendFrom {
        owner: String,
        contract: String,
        amount: Uint128,
        msg: Binary,
     }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "send_from":{
    "owner":"terra1...",
    "contract":"terra1...",
    "amount":"100",
    "msg": null
    }
 }
```
{% endtab %}
{% endtabs %}

| Name       | Type    | Description                                                  |
| ---------- | ------- | ------------------------------------------------------------ |
| `owner`    | String  | The owner address that has the tokens to transfer.           |
| `contract` | String  | The contract address to receive the tokens.                  |
| `amount`   | Uint128 | The amount of tokens to send from the owner to the contract. |
| `msg`      | Binary  |  A message to be sent to the receiving contract.             |

### BurnFrom

Burns a specified `amount` of tokens from the `owner` address.

{% hint style="info" %}
The `amount specified` cannot exceed the allowance of the address executing `BurnFrom.`
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
 BurnFrom {
        owner: String,
        amount: Uint128,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "burn_from":{
   "owner":"terra1...",
   "amount": "100",
     }
 }
```
{% endtab %}
{% endtabs %}

| Name     | Type    | Description                      |
| -------- | ------- | -------------------------------- |
| `owner`  | String  | The address to burn tokens from. |
| `amount` | Uint128 | The amount of tokens to burn.    |

### UpdateMarketing

Updates the marketing information if instantiated.&#x20;

{% hint style="info" %}
Setting None/null for any of these will leave it unchanged
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
UpdateMarketing {
        project: Option<String>,
        description: Option<String>,
        marketing: Option<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "update_marketing":{
  "project":"http..."
  "marketing":"terra1..."
    }
 }
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                        |
| ------------- | --------------- | ---------------------------------------------------------------------------------- |
| `project`     | Option\<String> | A URL pointing to the project behind this token.                                   |
| `description` | Option\<String> | A longer description of the token and it's utility. Designed for tooltips or such. |
| `marketing`   | Option\<String> | The address (if any) who can update this data structure.                           |

### UpdateOwner/UpdateOperators

Check [AndrReceive](../andrreceive-andrquery.md).

### RegisterModule/DeregisterModule/AlterModule

Check [AndrReceive](../andrreceive-andrquery.md).

## QueryMsg

### Balance

Queries the balance of a specific `address.`

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   Balance {
    address:String,
     }
 }

```
{% endtab %}

{% tab title="JSON" %}
```json
{
"balance":{
    "address":"terra1...",
   }
 }
 
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                        |
| ------- | ------ | ---------------------------------- |
| address | String | The address to get the balance of. |

#### BalanceResponse

{% tabs %}
{% tab title="Rust" %}
```rust
 pub struct BalanceResponse {
    pub balance: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"balance":"100"
}

```
{% endtab %}
{% endtabs %}

| Name    | Type    | Description                                                        |
| ------- | ------- | ------------------------------------------------------------------ |
| balance | Uint128 | The amount of tokens the specified `address` has in their balance. |

### TokenInfo

Returns metadata on the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
TokenInfo {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"token_info":{}
}
```
{% endtab %}
{% endtabs %}

#### TokenInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TokenInfoResponse {
    pub name: String,
    pub symbol: String,
    pub decimals: u8,
    pub total_supply: Uint128,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"name":"mytoken",
"symbol":"MYT",
"decimals": 6,
"total_supply":"10000000"
}
```
{% endtab %}
{% endtabs %}

| Name           | Type    | Description                           |
| -------------- | ------- | ------------------------------------- |
| `name`         | String  | The name of the token.                |
| `symbol`       | String  | The symbol of the token.              |
| `decimals`     | u8      | The number of decimals for the token. |
| `total_supply` | Uint128 | The total amount of tokens.           |

### Minter

Returns who can mint and the hard cap on maximum tokens after minting

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
Minter {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"minter":{}
}
```
{% endtab %}
{% endtabs %}

#### MinterResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MinterResponse {
    pub minter: String,
    pub cap: Option<Uint128>,
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"minter":"terra1...",
"cap":"1000000",
}
```
{% endtab %}
{% endtabs %}

| Name     | Type             | Description                                                        |
| -------- | ---------------- | ------------------------------------------------------------------ |
| `minter` | String           | The address of the assigned minter.                                |
| `cap`    | Option\<Uint128> | Cap is a hard cap on total supply that can be achieved by minting. |

### Allowance

Returns the amount of tokens that the spender has from the owner's tokens and the expiration for the tokens.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  Allowance {
  owner: String,
  spender: String 
   }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"allowance": {
   "owner":"terra1...",
   "spender":"terra1..."
   }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                             |
| ------- | ------ | --------------------------------------- |
| owner   | String | The address of the owner of the tokens. |
| spender | String | The address to check the allowance of.  |

#### AllowanceResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllowanceResponse {
    pub allowance: Uint128,
    pub expires: Expiration,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"allowance":"1000",
"expires":{
  "at_height":"500"
    }
}   

```
{% endtab %}
{% endtabs %}

| Name        | Type       | Description                                        |
| ----------- | ---------- | -------------------------------------------------- |
| `allowance` | Uint128    | The amount of tokens the spender has as allowance. |
| `expires`   | Expiration | The expiration for the tokens.                     |

### AllAllowanaces

Returns all the allowances that the specified `owner` has given along with the information for each allowance.

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum QueryMsg{
  AllAllowances {
        owner: String,
        start_after: Option<String>,
        limit: Option<u32>,
      }
   }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_allowances"{
  "owner":"terra1...",
  "limit": 20,
  "start_after": 4,
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                          |
| ------------- | --------------- | -------------------------------------------------------------------------------------------------------------------- |
| `owner`       | String          | The address of the owner that has given allowances.                                                                  |
| `start_after` | Option\<String> | An optional ID for which to start after, used for pagination.                                                        |
| `limit`       | Option\<u32>    | An optional limit to how many allowances are returned. Is set by default to 10. Can be set to reach a maximum of 30. |

#### AllAllowanceResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllAllowancesResponse {
    pub allowances: Vec<AllowanceInfo>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"allowances":[
     { 
        "spender":"terra1...",
        "allowance":"1000",
        "expires":{
              "never":{}
              }
           },
           {
           ...
           }
        ]
  }
     
```
{% endtab %}
{% endtabs %}

| Name         | Type                | Description                                                     |
| ------------ | ------------------- | --------------------------------------------------------------- |
| `allowances` | Vec\<AllowanceInfo> | A vector containing each allowance and the related information. |

#### AllowanceInfo

```rust
pub struct AllowanceInfo {
    pub spender: String,
    pub allowance: Uint128,
    pub expires: Expiration,
}
```

| Name        | Type       | Description                            |
| ----------- | ---------- | -------------------------------------- |
| `spender`   | String     | The address that has an allowance.     |
| `allowance` | Uint128    | The amount of tokens in the allowance. |
| `expires`   | Expiration | The expiration for the allowance.      |

### AllAccounts

Returns all the addresses that have a balance.

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum QueryMsg{
  AllAccounts {
        start_after: Option<String>,
        limit: Option<u32>,
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_accounts":{
 "limit": 25,
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                        |
| ------------- | --------------- | ------------------------------------------------------------------------------------------------------------------ |
| `start_after` | Option\<String> | An optional ID for which to start after, used for pagination.                                                      |
| `limit`       | Option\<u32>    | An optional limit to how many Accounts are returned. Is set by default to 10. Can be set to reach a maximum of 30. |

#### AllAccountsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllAccountsResponse {
    pub accounts: Vec<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"accounts":["terra1...","terra2...",...]
}
```
{% endtab %}
{% endtabs %}

| Name       | Type         | Description                                           |
| ---------- | ------------ | ----------------------------------------------------- |
| `accounts` | Vec\<String> | A vector containing the addresses that own the token. |

### MarketingInfo

Returns the metadata of the marketing of  the project.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
MarketingInfo {}
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"marketing_info":{}
}
```
{% endtab %}
{% endtabs %}

#### MarketingInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MarketingInfoResponse {
    pub project: Option<String>,
    pub description: Option<String>,
    pub logo: Option<LogoInfo>,
    pub marketing: Option<Addr>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"project":"http:...",
"description":" This project ...",
"marketing":"terra1..."
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                        |
| ------------- | --------------- | ---------------------------------------------------------------------------------- |
| `project`     | Option\<String> | A URL pointing to the project behind this token.                                   |
| `description` | Option\<String> | A longer description of the token and it's utility. Designed for tooltips or such. |
| `logo`        | Option\<String> | A link to the logo, or a comment there is an on-chain logo stored.                 |
| `marketing`   | Option\<Addr>   | The address (if any) who can update this data structure.                           |

### DownloadLogo

Downloads the embedded logo data (if stored on chain). Errors if no logo data stored for this contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
DownloadLogo {}
  }

```
{% endtab %}

{% tab title="JSON" %}
```json
{
"download_logo":{}
}
```
{% endtab %}
{% endtabs %}

#### DownloadLogoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct DownloadLogoResponse {
    pub mime_type: String,
    pub data: Binary,
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                |
| ----------- | ------ | -------------------------- |
| `mime_type` | String | The MIME type of the logo. |
| `data`      | Binary | The LOGO data.             |