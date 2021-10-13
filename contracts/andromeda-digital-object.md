---
description: The message definitions for the Andromeda Token contract
---

# Andromeda Token

### InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub minter: String,
    pub modules: Vec<ModuleDefinition>,
    pub init_hook: Option<InitHook>,
    pub metadata_limit: Option<u64>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "name": "Example Token",
    "symbol": "ET",
    "minter": "terra1...",
    "modules": [
        "whitelist": {
            "moderators": ["terra1..."]
        },
        "taxable": {
            "tax": 4,
            "receivers": ["terra1..."]
        }
    ],
    "metadata_limit": 10000
}
```
{% endtab %}
{% endtabs %}

| Name           | Type                   | Description                                                                                 |
| -------------- | ---------------------- | ------------------------------------------------------------------------------------------- |
| name           | String                 | The name of the NFT                                                                         |
| symbol         | String                 | The symbol of the NFT                                                                       |
| minter         | String                 | The address of the ADO minter                                                               |
| modules        | Vec\<ModuleDefinition> | A vector of Andromeda Module definitions. The module definitions can be found here.         |
| init_hook      | Option\<InitHook>      | An optional instantiation hook message, primarily used by the `andromeda_factory` contract  |
| metadata_limit | Option\<u64>           | An optional size limit on any metadata assigned to an NFT minted in the contract (in bytes) |

### ExecuteMsg

#### MintMsg

Mints a new ADO, only available to the defined `minter` in the contract's `InstantiateMsg`

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MintMsg {
    pub token_id: String,
    pub owner: String,
    pub name: String,
    pub description: Option<String>,
    pub metadata: Option<String>,
}

pub enum ExecuteMsg {
    Mint(MintMsg)
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "mint": {
        "token_id": "anewtoken",
        "owner": "terra1...",
        "name": "A New Token",
        "description": "A newly minted token",
        "metadata": "{'some_json_field': 'some_json_value_stringified'}"
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                     |
| ----------- | --------------- | --------------------------------------------------------------- |
| token_id    | String          | The id of the ADO to be minted                                  |
| owner       | String          | The address of the ADO owner                                    |
| name        | String          | The ADO's name                                                  |
| description | Option\<String> | An optional description of the ADO                              |
| metadata    | Option\<String> | An option string field storing some metadata related to the ADO |

#### TransferNft

A CW721 compliant transfer method. Transfers ownership of a minted token. Only available to the ADO owner, an approved operator or the purchaser in a `TransferAgreement` for the given ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    TransferNft {
        recipient: String,
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "transfer_nft": {
        "recipient": "terra1...",
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                         |
| --------- | ------ | ----------------------------------- |
| recipient | String | The address of the recipient        |
| token_id  | String | The id of the ADO to be transferred |

#### SendNft

A CW721 compliant send method. Sends ownership of a minted token to an external contract. Only available to the ADO owner or an approved operator for the given ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    SendNft {
        contract: String,
        token_id: String,
        msg: Binary,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "send_nft": {
        "contract": "terra1...",
        "token_id": "anewtoken",
        "msg": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                    |
| -------- | ------ | ---------------------------------------------- |
| contract | String | The address of the receiving contract          |
| token_id | String | The id of the ADO to be sent                   |
| msg      | Binary | A message to be sent to the receiving contract |

#### Approve

A CW721 compliant approve method. Approves a given address as an operator for the ADO, allowing them to transfer, burn or archive the ADO. Only available to the ADO owner.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Approve {
        spender: String,
        token_id: String,
        expires: Option<Expiration>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "approve": {
        "spender": "terra1...",
        "token_id": "anewtoken",
        "expires": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                | Description                                                        |
| -------- | ------------------- | ------------------------------------------------------------------ |
| spender  | String              | The address to be authorised as an operator                        |
| token_id | String              | The id of the ADO for which to assign the `spender` as an operator |
| expires  | Option\<Expiration> | An optional expiration for the approval                            |

#### Revoke

A CW721 compliant revoke method. Revokes operator privileges for a given address. Only available to the ADO owner.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Revoke {
        spender: String,
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "revoke": {
        "spender": "terra1...",
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                                |
| -------- | ------ | ---------------------------------------------------------- |
| spender  | String | The address of the operator for which to revoke privileges |
| token_id | String | The id of the ADO for which to revoke operator privileges  |

#### ApproveAll

A CW721 compliant approve all method. Approves a given address as an operator for all ADOs owned by the sender.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    ApproveAll {
        operator: String,
        expires: Option<Expiration>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "approve_all": {
        "operator": "terra1...",
        "expires": {
            "never": {}
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                | Description                                 |
| -------- | ------------------- | ------------------------------------------- |
| operator | String              | The address to be authorised as an operator |
| expires  | Option\<Expiration> | An optional expiration for the approval     |

#### RevokeAll

A CW721 compliant revoke all method. Revokes an operator's privileges for any ADOs owned by the sender.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    RevokeAll {
        operator: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "revoke_all": {
        "operator": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description                                                |
| -------- | ------ | ---------------------------------------------------------- |
| operator | String | The address of the operator for which to revoke privileges |

#### TransferAgreement

Allows an ADO owner to generate a transfer agreement for an owned ADO. This agreement allows the purchaser to transfer ownership of the ADO provided the correct funds are provided in a `TransferNft` message from the purchaser. The current owner of the ADO will receive the sent funds minus any required financial payments assigned to the contract via modules.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    TransferAgreement {
        token_id: String,
        denom: String,
        amount: u128,
        purchaser: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "transfer_agreement": {
        "token_id": "anewtoken",
        "denom": "uluna",
        "amount": 100,
        "purchaser": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| token_id  | String | The id of the ADO for which the agreement is made |
| denom     | String | The agreed amount's denomination                  |
| amount    | u128   | The agreed transfer amount                        |
| purchaser | String | The address of the transfer purchaser             |

#### Whitelist

{% hint style="warning" %}
Only available if the contract has an assigned `Whitelist` module
{% endhint %}

Whitelists or unwhitelists an address to be able to execute messages on the contract. 

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Whitelist {
        address: String,
        whitelisted: bool
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "whitelist": {
        "address": "terra1...",
        "whitelisted": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                      |
| ----------- | ------ | ------------------------------------------------ |
| address     | String | The address for which to modify whitelist status |
| whitelisted | bool   | Whether or not the address is to be whitelisted  |

#### Blacklist

{% hint style="warning" %}
Only available if the contract has an assigned `Blacklist` module
{% endhint %}

Blacklists or unblacklists an address to be unable to execute messages on the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Blacklist {
        address: String,
        blacklisted: bool
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "blacklist": {
        "address": "terra1...",
        "whitelisted": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                                      |
| ----------- | ------ | ------------------------------------------------ |
| address     | String | The address for which to modify blacklist status |
| blacklisted | bool   | Whether or not the address is to be blacklisted  |

### QueryMsg

#### OwnerOf

A CW721 compliant "owner of" query. Queries the current owner of a given ADO id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    OwnerOf {
        token_id: String
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "owner_of": {
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description               |
| -------- | ------ | ------------------------- |
| token_id | String | The id of the queried ADO |

#### OwnerOfResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct OwnerOfResponse {
    /// Owner of the token
    pub owner: String,
    /// If set this address is approved to transfer/send the token as well
    pub approvals: Vec<Approval>,
}

```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "owner": "terra1...",
    "approvals": [
        {
            "spender": "terra1...",
            "expires": {
                "never": {}
            }
        }
    ]
}
```
{% endtab %}
{% endtabs %}

| Name      | Type           | Description                           |
| --------- | -------------- | ------------------------------------- |
| owner     | String         | The owner of the queried ADO          |
| approvals | Vec\<Approval> | An array of all approvals for the ADO |

#### ApprovedForAll

A CW721 compliant "approved for all" query. Queries any operators for a given address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ApprovedForAll {
        owner: String,
        include_expired: Option<bool>,
        start_after: Option<String>,
        limit: Option<u32>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "approved_for_all": {
        "owner": "terra1...",
        "include_expired": true,
        "limit": 10
    }
}
```
{% endtab %}
{% endtabs %}

| Name            | Type            | Description                                                                              |
| --------------- | --------------- | ---------------------------------------------------------------------------------------- |
| owner           | String          | The address of the owner for which to query operators                                    |
| include_expired | Option\<bool>   | Whether to include any expired approvals. Defaults to false (if not defined in message). |
| limit           | Option\<u64>    | An optional limit on how many approvals are returned                                     |
| start_after     | Option\<String> | An optional address for which to start after, used for pagination.                       |

#### ApprovedForAllResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct ApprovedForAllResponse {
    pub operators: Vec<Approval>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "operators": [
        {
            "spender": "terra1...",
            "expires": {
                "never": {}
            }
        }
    ]
}
```
{% endtab %}
{% endtabs %}

| Name      | Type           | Description                                           |
| --------- | -------------- | ----------------------------------------------------- |
| approvals | Vec\<Approval> | An array of all approvals for the given owner address |

#### NumTokens

A CW721 compliant "num tokens" query. Queries the amount of ADOs minted by the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    NumTokens {}
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "num_tokens": {}
}
```
{% endtab %}
{% endtabs %}

#### NumTokensResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct NumTokensResponse {
    pub count: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "count": 1
}
```
{% endtab %}
{% endtabs %}

| Name  | Type | Description                               |
| ----- | ---- | ----------------------------------------- |
| count | u64  | The amount of ADOs minted by the contract |

#### NftInfo

A CW721 compliant "nft info" query. Queries the stored info of an ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    NftInfo {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "nft_info": {
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description         |
| -------- | ------ | ------------------- |
| token_id | String | The id of the token |

#### NftInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct NftInfoResponse {
    pub name: String,
    pub description: String,
    pub image: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "name": "A New Token",
    "description": "A newly minted token",
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                                                                                                                                              |
| ----------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name        | String          | The name of the ADO                                                                                                                                                                                      |
| description | String          | The description of the ADO                                                                                                                                                                               |
| image       | Option\<String> | A URI pointing to a resource with mime type image/\* representing the asset to which this NFT represents. (Taken from [here](https://github.com/CosmWasm/cw-plus/blob/main/packages/cw721/src/query.rs)) |

#### AllNftInfo

A CW721 compliant "all nft info" query. Queries all stored info of an ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    AllNftInfo {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "all_nft_info": {
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description       |
| -------- | ------ | ----------------- |
| token_id | String | The id of the ADO |

#### AllNftInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AllNftInfoResponse {
    pub access: OwnerOfResponse,
    pub info: NftInfoResponse,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "access": {
        "owner": "terra1...",
        "approvals": [
            {
                "spender": "terra1...",
                "expires": {
                    "never": {}
                }
            }
        ]
    },
    "info": {
        "name": "A New Token",
        "description": "A newly minted token",
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type            | Description                            |
| ------ | --------------- | -------------------------------------- |
| access | OwnerOfResponse | The owner of the ADO and any approvals |
| info   | NFtInfoResponse | The given ADO's stored information     |

#### NftTransferAgreementInfo

Queries the current transfer agreement for an ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    NftTransferAgreementInfo {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "nft_transfer_agreement_info": {
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description       |
| -------- | ------ | ----------------- |
| token_id | String | The id of the ADO |

#### NftTransferAgreementResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct NftTransferAgreementResponse {
    pub agreement: Option<TransferAgreement>
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "agreement": {
        "amount": "100uluna",
        "purchaser": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                       | Description                                                              |
| --------- | -------------------------- | ------------------------------------------------------------------------ |
| agreement | Option\<TransferAgreement> | The transfer agreement for the given ADO, undefined if no agreement set. |

#### NftMetadata

Queries the  metadata field for a given ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    NftMetadata {
        token_id: String
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "nft_metadata": {
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type   | Description       |
| -------- | ------ | ----------------- |
| token_id | String | The id of the ADO |

#### NftMetadataResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct NftMetadataResponse{
    pub metadata: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "metadata": "{'some_json_key': 'some_json_stringified_value'}"
}
```
{% endtab %}
{% endtabs %}

| Name     | Type            | Description                       |
| -------- | --------------- | --------------------------------- |
| metadata | Option\<String> | The assigned metadata for the ADO |

#### ContractInfo

Queries the  metadata field for a given token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ContractInfo {}
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "contract_info": {}
}
```
{% endtab %}
{% endtabs %}

#### ContractInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct CotractInfoResponse {
    pub name: String,
    pub symbol: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "name": "Example Token",
    "symbol": "ET"
}
```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| name   | String | The name of the contract            |
| symbol | String | The assigned symbol of the contract |
