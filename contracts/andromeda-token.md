---
description: The message definitions for Andromeda Contracts
---

# Andromeda Token

## Andromeda Token

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
    "instantiate_msg": {
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
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| name | String | The name of the NFT |
| symbol | String | The symbol of the NFT |
| minter | String | The address of the token minter |
| modules | Vec&lt;ModuleDefinition&gt; | A vector of Andromeda Module definitions. The module definitions can be found here. |
| init\_hook | Option&lt;InitHook&gt; | An optional instantiation hook message, primarily used by the `andromeda_factory` contract |
| metadata\_limit | Option&lt;u64&gt; | An optional size limit on any metadata assigned to an NFT minted in the contract \(in bytes\) |

### ExecuteMsg

#### MintMsg

Mints a new token, only available to the defined `minter` in the contract's `InstantiateMsg`

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
        "metadata": "{'some_json_field': 'some_json_key_stringified'}"
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| token\_id | String | The id of the token to be minted |
| owner | String | The address of the token owner |
| name | String | The token's name |
| description | Option&lt;String&gt; | An optional description of the token |
| metadata | Option&lt;String&gt; | An option string field storing some metadata related to the token |

#### TransferNft

A CW721 compliant transfer method. Transfers ownership of a minted token. Only available to the token owner, an approved operator or the purchaser in a `TransferAgreement` for the given token.

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

| Name | Type | Description |
| :--- | :--- | :--- |
| recipient | String | The address of the recipient |
| token\_id | String | The id of the token to be transferred |

#### SendNft

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
        "contract": "terra1...",
        "token_id": "anewtoken",
        "msg": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| contract | String | The address of the receiving contract |
| token\_id | String | The id of the token to be sent |
| msg | Binary | A message to be sent to the receiving contract |

#### Approve

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
        "spender": "terra1...",
        "token_id": "anewtoken",
        "expires": null
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| spender | String | The address to be authorised as an operator |
| token\_id | String | The id of the token for which to assign the `spender` as an operator |
| expires | Option&lt;Expiration&gt; | An optional expiration for the approval |

#### Revoke

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
        "spender": "terra1...",
        "token_id": "anewtoken"
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| spender | String | The address of the operator for which to revoke privileges |
| token\_id | String | The id of the token for which to revoke operator privileges |

#### ApproveAll

A CW721 compliant approve all method. Approves a given address as an operator for all tokens owned by the sender.

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

| Name | Type | Description |
| :--- | :--- | :--- |
| operator | String | The address to be authorised as an operator |
| expires | Option&lt;Expiration&gt; | An optional expiration for the approval |

#### RevokeAll

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
        "operator": "terra1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| operator | String | The address of the operator for which to revoke privileges |

#### TransferAgreement

Allows a token owner to generate a transfer agreement for an owner token. This agreement allows the purchaser to transfer ownership of the token provided the correct funds are provided. The current owner of the token will receive the sent funds minus any required financial payments assigned to the contract via modules.

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

| Name | Type | Description |
| :--- | :--- | :--- |
| token\_id | String | The id of the token for which the agreement is made |
| denom | String | The agreed amount's denomination |
| amount | u128 | The agreed transfer amount |
| purchaser | String | The address of the transfer purchaser |

#### Whitelist

Whitelists or unwhitelists an address to be able to execute messages on the contract. **Only availabe if the `Whitelist` module is assigned to the contract.**

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

| Name | Type | Description |
| :--- | :--- | :--- |
| address | String | The address for which to modify whitelist status |
| whitelisted | bool | Whether or not the address is to be whitelisted |

#### Blacklist

Blacklists or unblacklists an address to be unable to execute messages on the contract. **Only availabe if the `Blacklist` module is assigned to the contract.**

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
    "whitelist": {
        "address": "terra1...",
        "whitelisted": true
    }
}
```
{% endtab %}
{% endtabs %}

| Name | Type | Description |
| :--- | :--- | :--- |
| address | String | The address for which to modify blacklist status |
| blacklisted | bool | Whether or not the address is to be blacklisted |



