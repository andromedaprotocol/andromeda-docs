# NFT Collectible

## Introduction

The **NFT Collectible** ADO is a smart contract to allow users to launch their own custom NFT projects. In addition to the standard CW721 messages, we have added some custom logic to further extend the utility and function of the contract.&#x20;

It supports the use of all our [modules ](../modules/module-definitions.md#module-definitions)that can be attached to the contract upon instantiation and modified to satisfy the project needs. The [Offers](../modules/offers-module/) Module is specifically created to work with this contract to facilitate the process of buying/selling the tokens.

In addition to the offers module, the contract has implemented a custom `TransferAgreement` message to allow the buying/selling of tokens between two parties if the Offers module is not to be used.

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, Debug, PartialEq, JsonSchema)]
pub struct InstantiateMsg {
    pub name: String,
    pub symbol: String,
    pub minter: AndrAddress,
    pub modules: Option<Vec<Module>>,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "name": "Example Token",
    "symbol": "ET",
    "minter":{
        "addr":"juno1..."
        },
   "modules": [
        {
          "module_type": "address_list",
          "address": {
          "identifier":"juno1..."
          }
          "is_mutable": false
        },
        {
        ...
        }
      ]
  }
        
```
{% endtab %}
{% endtabs %}

| Name      | Type                                                    | Description                                                                                                            |
| --------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `name`    | String                                                  | The name of the token. Has to be between 3 and 30 characters.                                                          |
| `symbol`  | String                                                  | The symbol of the token.                                                                                               |
| `minter`  | [AndrAddress](../common-types/recipient.md#andraddress) | The address of the token minter.                                                                                       |
| `modules` | Option\<Vec<[Module](broken-reference)>>                | An optional vector of Andromeda Modules. The module definitions can be found[ here](../modules/module-definitions.md). |

## ExecuteMsg

## Mint

Mints a new ADO, only available to the defined `minter` in the contract's `InstantiateMsg`

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MintMsg<T> {
    pub token_id: String,
    pub owner: String,
    pub token_uri: Option<String>,
    pub extension: T,
}

pub enum ExecuteMsg {
    Mint(box<MintMsg<TokenExtension>>)
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "mint": {
        "token_id": "anewtoken",
        "owner": "juno1...",
        "extension":{
         "name":"mytoken",
         "publisher":"publisher",
         "description":"This token ....",
         "archived": false
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type              | Description                                                                                                                |
| ----------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `token_id`  | String            | The id of the token to be minted.                                                                                          |
| `owner`     | String            | The address of the token owner.                                                                                            |
| `token_uri` | Option\<String>   | Universal resource identifier for this token. Should point to a JSON file that conforms to the CW721 Metadata JSON Schema. |
| `extension` | T  (Generic type) | Any custom extension used by this contract. Her we use [TokenExtension](andromeda-digital-object.md#tokenextension).       |

### TokenExtension

Extension that can be added to an ADO when minting.

```rust
pub struct TokenExtension {
    pub name: String,
    pub publisher: String,
    pub description: Option<String>,
    pub transfer_agreement: Option<TransferAgreement>,
    pub metadata: Option<TokenMetadata>,
    pub archived: bool,
}
```

| Name                 | Type                                                                       | Description                                         |
| -------------------- | -------------------------------------------------------------------------- | --------------------------------------------------- |
| `name`               | String                                                                     | The name of the token.                              |
| `publisher`          | String                                                                     | The original publisher of the token (immutable).    |
| `description`        | Option\<String>                                                            | An optional description of the token.               |
| `transfer_agreement` | Option<[TransferAgreement](andromeda-digital-object.md#transferagreement)> | The transfer agreement of the token (if it exists). |
| `metadata`           | Option<[TokenMetadata](andromeda-digital-object.md#metadata-schema)>       | The metadata of the token (if it exists)            |
| `archived`           | bool                                                                       | Whether the token is archived or not.               |

### TransferAgreement

A struct used to represent an agreed transfer of a token. The `purchaser` may use the `Transfer` message for this token as long as funds are provided equaling the `amount` defined in the agreement.

If the `purchaser` is set to `"*"` then anyone can complete the `TransferAgreement` (Anyone can buy the token)

```rust
pub struct TransferAgreement {
    pub amount: Value<Coin>,
    pub purchaser: String,
}
```

| Name        | Type                                   | Description                                                               |
| ----------- | -------------------------------------- | ------------------------------------------------------------------------- |
| `amount`    | Value<[Coin](../common-types/coin.md)> | The amount required for the purchaser to transfer ownership of the token. |
| `purchaser` | String                                 | The address of the purchaser.                                             |

#### `Value<T>`

```rust
pub enum Value<T>
where
    // This restriction is to ensure that `T` is a type that can be stored as a `Primitive`. It
    // could work without, but we could easily get cases where `T` cannot be a `Primitive` and this
    // gives us compile-time insurance.
    T: Into<Primitive>,
{
    /// The raw value.
    Raw(T),
    /// The pointer to the primitive. This SHOULD be of the same underlying type as `T`. For
    /// example, if `T` is `String`, then `PrimitivePointer` should point to a Primitive::String(..).
    /// This cannot be enforced at compile time though, so it is up to the discretion of the user.
    Pointer(PrimitivePointer),
}
```

#### PrimitivePointer

```rust
pub struct PrimitivePointer {
    pub address: AndrAddress,
    pub key: Option<String>,
}
```

| Name      | Type                                                    | Description                            |
| --------- | ------------------------------------------------------- | -------------------------------------- |
| `address` | [AndrAddress](../common-types/recipient.md#andraddress) | The address of the primitive contract. |
| `key`     | Option\<String>                                         | The optional key for the stored data.  |

### Metadata Schema for Mint

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
    pub display_label: Option<String>,
}

pub struct TokenMetadata {
    pub data_type: MetadataType,
    pub external_url: Option<String>,
    pub data_url: Option<String>,
    pub attributes: Option<Vec<MetadataAttribute>>,
}
```

### MetadataAttribute

Used to define any rich data attributes related to an ADO.

| Name            | Type            | Description                                                                                                |
| --------------- | --------------- | ---------------------------------------------------------------------------------------------------------- |
| `key`           | String          | The associated key for the attribute.                                                                      |
| `value`         | String          | The value for the attribute.                                                                               |
| `display_label` | Option\<String> | An optional string for how the key should be displayed. If none is provided the \`key\` field can be used. |

### TokenMetadata

| Name           | Type                             | Description                                                                                                    |
| -------------- | -------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `data_type`    | Metadatatype                     | The type of data related to the token, must be one of the following; Audio, Video, Image, Domain, Json, Other. |
| `external_url` | Option\<String>                  | An optional link to an external source for the token.                                                          |
| `data_url`     | Option\<String>                  | An optional link to the ADO's external data. Response should be of the type defined by the `data_type` field.  |
| `attributes`   | Option\<Vec\<MetadataAttribute>> | An array of rich data attributes. See [MetadataAttribute](andromeda-digital-object.md#metadataattribute).      |

### TransferNft

A CW721 compliant transfer method. Transfers ownership of a minted token. Only available to the token owner, an approved operator or the purchaser in a `TransferAgreement` for the given token.

{% hint style="warning" %}
Archived tokens cannot be transferred.
{% endhint %}

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
        "recipient": "juno1...",
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                            |
| ----------- | ------ | -------------------------------------- |
| `recipient` | String | The address of the recipient.          |
| `token_id`  | String | The id of the token to be transferred. |

### SendNft

A CW721 compliant send method. Sends ownership of a minted token to an external contract. Only available to the token owner or an approved operator for the given token.

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
        "contract": "juno1...",
        "token_id": "anewtoken",
        "msg": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                     |
| ---------- | ------ | ----------------------------------------------- |
| `contract` | String | The address of the receiving contract.          |
| `token_id` | String | The id of the token to be sent.                 |
| `msg`      | Binary | A message to be sent to the receiving contract. |

### Burn <a href="#mintmsg" id="mintmsg"></a>

Destroys any token data related to an token id. Only available to the token owner.

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

{% tab title="JSON" %}
```javascript
{
    "burn": {
        "token_id": "anewtoken",
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                  |
| ---------- | ------ | ---------------------------- |
| `token_id` | String | The id of the token to burn. |

### Archive <a href="#mintmsg" id="mintmsg"></a>

Archives an token, making it immutable in any respect. Once an token is archived it cannot be edited, transferred or burnt. Only available to the token owner.

{% hint style="danger" %}
Cannot be undone
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Archive {
        token_id: String,
    }
}
```
{% endtab %}

{% tab title="Json" %}
```javascript
{
    "archive": {
        "token_id": "anewtoken",
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                   |
| ---------- | ------ | ----------------------------- |
| `token_id` | String | The id of the ADO to archive. |

### Approve

A CW721 compliant approve method. Approves a given address as an operator for the token, allowing them to transfer, burn or archive the token. Only available to the token owner.

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
        "spender": "juno1...",
        "token_id": "anewtoken",
        "expires": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                | Description                                                        |
| ---------- | --------------------------------------------------- | ------------------------------------------------------------------ |
| `spender`  | String                                              | The address to be authorised as an operator                        |
| `token_id` | String                                              | The id of the ADO for which to assign the `spender` as an operator |
| `expires`  | Option<[Expiration](../common-types/expiration.md)> | An optional expiration for the approval                            |

### Revoke

A CW721 compliant revoke method. Revokes operator privileges for a given address. Only available to the token owner.

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
        "spender": "juno1...",
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                                 |
| ---------- | ------ | ----------------------------------------------------------- |
| `spender`  | String | The address of the operator for which to revoke privileges  |
| `token_id` | String | The id of the token for which to revoke operator privileges |

### ApproveAll

A CW721 compliant approve all method. Approves a given address as an operator for all tokens owned by the sender.

{% hint style="warning" %}
Will overwrite any approval currently assigned to the operator's address.
{% endhint %}

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
        "operator": "juno1...",
        "expires": {
            "never": {}
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                                                | Description                                 |
| ---------- | --------------------------------------------------- | ------------------------------------------- |
| `operator` | String                                              | The address to be authorised as an operator |
| `expires`  | Option<[Expiration](../common-types/expiration.md)> | An optional expiration for the approval     |

### RevokeAll

A CW721 compliant revoke all method. Revokes an operator's privileges for any tokens owned by the sender.

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
        "operator": "juno1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                                |
| ---------- | ------ | ---------------------------------------------------------- |
| `operator` | String | The address of the operator for which to revoke privileges |

### TransferAgreement

Allows a token owner to generate a transfer agreement for an owned token. This agreement allows the purchaser to transfer ownership of the token provided the correct funds are provided in a `TransferNft` message from the purchaser. The current owner of the token will receive the sent funds minus any required financial payments assigned to the contract via modules.

{% hint style="warning" %}
Will overwrite any current transfer agreement for the token.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    TransferAgreement {
        token_id: String,
        agreement:Option<TransferAgreement>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
"transfer_agreement": {
      "token_id": "anewtoken",
      "agreement":{
          "amount":{
            "raw":{
                   "denom":"uusd",
                   "amount":"100000"
           }
   },
           "purchaser":"juno1..."
           }
    }
}

or

{
"transfer_agreement": {
      "token_id": "anewtoken",
      "agreement":{
          "amount":{
            "pointer":{
                   "address":"juno1...",
                   "key":"price"
           }
   },
           "purchaser":"juno1..."
           }
    }
}

```
{% endtab %}
{% endtabs %}

| Name        | Type                                                                       | Description                                                     |
| ----------- | -------------------------------------------------------------------------- | --------------------------------------------------------------- |
| `token_id`  | String                                                                     | The id of the token for which the agreement is made.            |
| `agreement` | Option<[TransferAgreement](andromeda-digital-object.md#transferagreement)> | See [TransferAgreement](andromeda-digital-object.md#undefined). |

### AndrReceive

{% hint style="info" %}
Uses the modules feature.
{% endhint %}

Check [AndrReceive](../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### Minter

Queries the current minter of the contract.

```rust
pub enum QueryMsg{
    Minter{},
 }
```

#### MinterResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct MinterResponse{
minter:String,
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"minter":"juno1..."
}
```
{% endtab %}
{% endtabs %}

### OwnerOf

A CW721 compliant "owner of" query. Queries the current owner of a given token id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    OwnerOf {
        token_id: String,
        include_expired:bool,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "owner_of": {
        "token_id": "anewtoken",
        "include_expired": false
    }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type   | Description                            |
| ----------------- | ------ | -------------------------------------- |
| `token_id`        | String | The id of the queried token.           |
| `include_expired` | bool   | Whether to include any expired owners. |

#### OwnerOfResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[derive(Serialize, Deserialize, Clone, PartialEq, JsonSchema, Debug)]
pub struct OwnerOfResponse {
    pub owner: String,
    pub approvals: Vec<Approval>,
}

```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "owner": "juno1...",
    "approvals": [
        {
            "spender": "juno1...",
            "expires": {
                "never": {}
            }
        }
    ]
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                  | Description                              |
| ----------- | ----------------------------------------------------- | ---------------------------------------- |
| `owner`     | String                                                | The owner of the queried token.          |
| `approvals` | Vec<[Approval](andromeda-digital-object.md#approval)> | An array of all approvals for the token. |

#### Approval

```rust
pub struct Approval {
    pub spender: String,
    pub expires: Expiration,
}
```

| Name      | Type                                        | Description                      |
| --------- | ------------------------------------------- | -------------------------------- |
| `spender` | String                                      | The address that is approved.    |
| `expires` | [Expiration](../common-types/expiration.md) | The expiration for the approval. |

### ApprovedForAll

A CW721 compliant "approved for all" query. Queries any operators for a given address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    ApprovedForAll {
        owner: String,
        include_expired: bool,
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
        "owner": "juno1...",
        "include_expired": true,
        "limit": 10
    }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type            | Description                                                        |
| ----------------- | --------------- | ------------------------------------------------------------------ |
| `owner`           | String          | The address of the owner for which to query operators.             |
| `include_expired` | bool            | Whether to include any expired approvals.                          |
| `limit`           | Option\<u32>    | An optional limit on how many approvals are returned.              |
| `start_after`     | Option\<String> | An optional address for which to start after, used for pagination. |

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
            "spender": "juno1...",
            "expires": {
                "never": {}
            }
        },
      ...
    ]
}
```
{% endtab %}
{% endtabs %}

| Name        | Type                                                   | Description                                            |
| ----------- | ------------------------------------------------------ | ------------------------------------------------------ |
| `approvals` | Vec<[Approval](andromeda-digital-object.md#undefined)> | An array of all approvals for the given owner address. |

### NumTokens

A CW721 compliant "num tokens" query. Queries the amount of tokens minted by the contract.

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

| Name    | Type | Description                                  |
| ------- | ---- | -------------------------------------------- |
| `count` | u64  | The amount of tokens minted by the contract. |

### NftInfo

A CW721 compliant "nft info" query. Queries the stored info of a token.

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

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `token_id` | String | The id of the token. |

#### NftInfoResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct NftInfoResponse<T> {
    pub token_uri: Option<String>,
    pub extension: T,
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
   "extension":{
         "name":"mytoken",
         "publisher":"publisher",
         "description":"This token ....",
         "archived": false
        }
 }
```
{% endtab %}
{% endtabs %}

| Name        | Type             | Description                                  |
| ----------- | ---------------- | -------------------------------------------- |
| `token_uri` | String           | Universal resource identifier for this ADO.  |
| `extension` | T (Generic type) | Any extension being used by the contract.    |

### AllNftInfo

A CW721 compliant "all nft info" query. Queries all stored info of an token.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    AllNftInfo {
        token_id: String,
        include_expired: Option<bool>
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "all_nft_info": {
        "token_id": "anewtoken",
        "include_expired": false
    }
}
```
{% endtab %}
{% endtabs %}

| Name              | Type          | Description                               |
| ----------------- | ------------- | ----------------------------------------- |
| `token_id`        | String        | The id of the ADO                         |
| `include_expired` | Option\<bool> | Whether to include any expired approvals. |

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
        "owner": "juno1...",
        "approvals": [
            {
                "spender": "juno1...",
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
                "purchaser": "juno1...",
                "amount": "100uluna"
            }
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                                           | Description                            |
| -------- | -------------------------------------------------------------- | -------------------------------------- |
| `access` | [OwnerOfResponse](andromeda-digital-object.md#ownerofresponse) | The owner of the ADO and any approvals |
| `info`   | [NFtInfoResponse](andromeda-digital-object.md#nftinforesponse) | The given ADO's stored information     |

### Tokens

Queries all the tokens of a particular owner.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    Tokens {
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
"tokens": {
            "owner":"juno1...",
            "limit": 25
            }
    }

```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                         |
| ------------- | --------------- | --------------------------------------------------------------------------------------------------- |
| `owner`       | String          | The address that we want to check the tokens of                                                     |
| `start_after` | Option\<String> | An optional address for which to start after, used for pagination.                                  |
| `limit`       | Option\<u32>    | Optional limit to the number of tokens queried. It is set by default as 10 and can be set up to 30. |

#### TokensResponse

Contains all token\_ids in lexicographical ordering. If there are more than `limit`, use `start_from` in future queries to achieve pagination.

```rust
pub struct TokensResponse {
    pub tokens: Vec<String>,
}
```

### AllTokens

Queries the tokens minted by the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum QueryMsg {
 AllTokens {
        start_after: Option<String>,
        limit: Option<u32>,
        }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "all_tokens": {
  
          "limit": 5
          
          }
  }
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                         |
| ------------- | --------------- | --------------------------------------------------------------------------------------------------- |
| `start_after` | Option\<String> | An optional address for which to start after, used for pagination.                                  |
| `limit`       | Option\<u32>    | Optional limit to the number of tokens queried. It is set by default as 10 and can be set up to 30. |

#### AllTokensResponse

Contains all token\_ids in lexicographical ordering. If there are more than `limit`, use `start_from` in future queries to achieve pagination.

```rust
pub struct TokensResponse {
    pub tokens: Vec<String>,
}
```

### ContractInfo

Queries the  name and symbol of  the token collection.

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

| Name     | Type   | Description                          |
| -------- | ------ | ------------------------------------ |
| `name`   | String | The name of the contract.            |
| `symbol` | String | The assigned symbol of the contract. |

The rest of the base Queries are found in [AndrQuery](../ado\_base/andrreceive-andrquery.md).
