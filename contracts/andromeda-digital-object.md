---
description: The message definitions for the Andromeda Token contract
---

# Andromeda Token

## Metadata Schema

A token can be minted with rich metadata using the following schema:

```rust
pub enum MetadataType {
    Image,
    Video,
    Audio,
    Domain,
    Json,
    Other,
}

pub struct MetadataAttribute {
    pub key: String,
    pub value: String,
    //The string used to display the attribute, if none is provided the `key` field can be used
    pub display_label: Option<String>,
}

pub struct TokenMetadata {
    pub data_type: MetadataType,
    //A URL to the token's source
    pub external_url: Option<String>,
    //A URL to any off-chain data relating to the token, the response from this URL should match the defined `data_type` of the token
    pub data_url: Option<String>,
    //On chain attributes related to the token (basic key/value)
    pub attributes: Option<Vec<MetadataAttribute>>,
}
```

### MetadataAttribute

Used to define any rich data attributes related to a token.

| Name           | Type            | Description                                             |
| -------------- | --------------- | ------------------------------------------------------- |
| key            | String          | The associated key for the attribute.                   |
| value          | String          | The value for the attribute.                            |
| display\_label | Option\<String> | An optional string for how the key should be displayed. |

### TokenMetadata

| Name          | Type                             | Description                                                                                                     |
| ------------- | -------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| data\_type    | Metadatatype                     | The type of data related to the token, must be one of the following; Audio, Video, Image, Domain, Json, Other   |
| external\_url | Option\<String>                  | An optional link to an external source for the Token.                                                           |
| data\_url     | Option\<String>                  | An optional link to the Token's external data. Response should be of the type defined by the `data_type` field. |
| attributes    | Option\<Vec\<MetadataAttribute>> | An array of rich data attributes. See [MetadataAttribute](andromeda-digital-object.md#metadataattribute).       |

## InstantiateMsg

{% hint style="info" %}
The defined `minter` address is assigned as the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub minter: String,
    pub modules: Vec<ModuleDefinition>,
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
            "rate": {
                "flat": {
                    "amount": 2,
                    "denom": "uluna"
                }
            },
            "receivers": ["terra1...", "terra1..."],
            "description": "Some tax payment to be made to..."
        }
    ],
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                   | Description                                                                            |
| ------- | ---------------------- | -------------------------------------------------------------------------------------- |
| name    | String                 | The name of the NFT                                                                    |
| symbol  | String                 | The symbol of the NFT                                                                  |
| minter  | String                 | The address of the ADO minter. Will be assigned as the [contract owner](ownership.md). |
| modules | Vec\<ModuleDefinition> | A vector of Andromeda Module definitions. The module definitions can be found here.    |

## ExecuteMsg

### Mint

Mints a new ADO, only available to the defined `minter` in the contract's `InstantiateMsg`

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MintMsg {
    pub token_id: String,
    pub owner: String,
    pub name: String,
    pub url: Option<String>,
    pub description: Option<String>,
    pub metadata: Option<TokenMetadata>,
    pub pricing: Option<Coin>,
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
        "image": "ipfs://...",
        "metadata": {
            "data_type": "Audio",
            "external_url": "https://...",
            "data_url": "https://...",
        },
        "pricing": {
            "uluna": 100,
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                   | Description                                                       |
| ----------- | ---------------------- | ----------------------------------------------------------------- |
| token\_id   | String                 | The id of the ADO to be minted                                    |
| owner       | String                 | The address of the ADO owner                                      |
| name        | String                 | The ADO's name                                                    |
| description | Option\<String>        | An optional description of the ADO                                |
| metadata    | Option\<TokenMetadata> | See [Token Metadata](andromeda-digital-object.md#token-metadata). |
| image       | Option\<String>        | The CW721 image field                                             |
| pricing     | Option\<Coin>          | Optional listing price for the token.                             |

### TransferNft

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
| token\_id | String | The id of the ADO to be transferred |

### SendNft

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

| Name      | Type   | Description                                    |
| --------- | ------ | ---------------------------------------------- |
| contract  | String | The address of the receiving contract          |
| token\_id | String | The id of the ADO to be sent                   |
| msg       | Binary | A message to be sent to the receiving contract |

### Burn <a href="#mintmsg" id="mintmsg"></a>

Destroys any token data related to a token id. Only available to the ADO owner.

{% hint style="danger" %}
Cannot be undone
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Burn {
        token_id: String,
    },
}
```
{% endtab %}

{% tab title="Second Tab" %}
```javascript
{
    "burn": {
        "token_id": "anewtoken",
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                 |
| --------- | ------ | --------------------------- |
| token\_id | String | The id of the token to burn |

### Archive <a href="#mintmsg" id="mintmsg"></a>

Archives a token, making it immutable in any respect. Once a token is archived it cannot be edited, transferred or burnt. Only available to the ADO owner.

{% hint style="danger" %}
Cannot be undone
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Archive {
        token_id: String,
    },
}
```
{% endtab %}

{% tab title="Second Tab" %}
```javascript
{
    "archive": {
        "token_id": "anewtoken",
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                    |
| --------- | ------ | ------------------------------ |
| token\_id | String | The id of the token to archive |

### Approve

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

| Name      | Type                | Description                                                        |
| --------- | ------------------- | ------------------------------------------------------------------ |
| spender   | String              | The address to be authorised as an operator                        |
| token\_id | String              | The id of the ADO for which to assign the `spender` as an operator |
| expires   | Option\<Expiration> | An optional expiration for the approval                            |

### Revoke

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

| Name      | Type   | Description                                                |
| --------- | ------ | ---------------------------------------------------------- |
| spender   | String | The address of the operator for which to revoke privileges |
| token\_id | String | The id of the ADO for which to revoke operator privileges  |

### ApproveAll

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

### RevokeAll

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

### TransferAgreement

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
| token\_id | String | The id of the ADO for which the agreement is made |
| denom     | String | The agreed amount's denomination                  |
| amount    | u128   | The agreed transfer amount                        |
| purchaser | String | The address of the transfer purchaser             |

### UpdatePricing

Allows an ADO owner to update or remove the listing price of their ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdatePricing {
        token_id: String,
        price: Option<Coin>
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "update_pricing": {
        "token_id": "anewtoken",
        "price": {
            "denom": "uluna",
            "amount": "100",
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type          | Description                                                                   |
| --------- | ------------- | ----------------------------------------------------------------------------- |
| token\_id | String        | The id of the ADO for which the agreement is made                             |
| price     | Option\<Coin> | The listing price for the token. If `None` token will be considered unlisted. |

### UpdateOwner

See [Ownership](ownership.md#executemsg).

## QueryMsg

### OwnerOf

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

| Name      | Type   | Description               |
| --------- | ------ | ------------------------- |
| token\_id | String | The id of the queried ADO |

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

### ApprovedForAll

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

| Name             | Type            | Description                                                                              |
| ---------------- | --------------- | ---------------------------------------------------------------------------------------- |
| owner            | String          | The address of the owner for which to query operators                                    |
| include\_expired | Option\<bool>   | Whether to include any expired approvals. Defaults to false (if not defined in message). |
| limit            | Option\<u64>    | An optional limit on how many approvals are returned                                     |
| start\_after     | Option\<String> | An optional address for which to start after, used for pagination.                       |

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

### NumTokens

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

### NftInfo

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

| Name      | Type   | Description         |
| --------- | ------ | ------------------- |
| token\_id | String | The id of the token |

#### NftInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct NftInfoResponse {
    pub name: String,
    pub description: String,
    pub image: Option<String>,
    pub extension: {
        metadata: Option<String>,
        archived: bool,
        transfer_agreement: Option<TransferAgreement>
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "name": "A New Token",
    "description": "A newly minted token",
    "extension": {
        "archived": false,
        "metadata": "{ \"some_json_field\": \"some_json_value\" }",
        "agreement": {
            "purchaser": "terra1...",
            "amount": "100uluna"
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                                                                                                                                              |
| ----------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name        | String          | The name of the ADO                                                                                                                                                                                      |
| description | String          | The description of the ADO                                                                                                                                                                               |
| image       | Option\<String> | A URI pointing to a resource with mime type image/\* representing the asset to which this NFT represents. (Taken from [here](https://github.com/CosmWasm/cw-plus/blob/main/packages/cw721/src/query.rs)) |
| extension   | Object          | Extended metadata related to the token.                                                                                                                                                                  |

### AllNftInfo

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

| Name      | Type   | Description       |
| --------- | ------ | ----------------- |
| token\_id | String | The id of the ADO |

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
        "extension": {
            "archived": false,
            "metadata": "{ \"some_json_field\": \"some_json_value\" }",
            "agreement": {
                "purchaser": "terra1...",
                "amount": "100uluna"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type            | Description                            |
| ------ | --------------- | -------------------------------------- |
| access | OwnerOfResponse | The owner of the ADO and any approvals |
| info   | NFtInfoResponse | The given ADO's stored information     |

### ModuleInfo

Queries module definitions for the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ModuleInfo {
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "module_info": {
    }
}
```
{% endtab %}
{% endtabs %}

#### ModuleInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ModuleInfoResponse {
    pub modules: Vec<ModuleDefinition>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "modules": [
        "receipt":{},
        "whitelist": {
            "address": "terra1...",
        },
        "taxable": {
            "rate": 2,
            "receivers": ["terra1..."]
        }
    ]
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                           | Description                              |
| ------- | ---------------------------------------------- | ---------------------------------------- |
| modules | Vec<[ModuleDefinition](../modules/modules.md)> | The module definitions for the contract. |

### ModuleContracts

Queries the assigned contracts for any modules.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ModuleContracts {
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "module_contracts": {
    }
}
```
{% endtab %}
{% endtabs %}

#### ModuleContractsResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ModuleContract {
    pub module: String,
    pub contract: Option<String>,
}

#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct ModuleContractsResponse {
    pub contracts: Vec<ModuleContract>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "contracts": [
        {
            "module": "tax",
            "contract": null,
        },
        {
            "module": "whitelist",
            "contract": "terra1...",
        }
    ]
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                 | Description                                                                          |
| --------- | -------------------- | ------------------------------------------------------------------------------------ |
| contracts | Vec\<ModuleContract> | A list of modules and their contract addresses (if they have an associated contract) |

### ContractInfo

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

#### ContractOwner

See [Ownership](ownership.md#querymsg).
