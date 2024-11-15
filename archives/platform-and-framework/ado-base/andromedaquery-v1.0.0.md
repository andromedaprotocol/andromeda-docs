---
description: The set of query messages common to all ADOs in the Andromeda Logic Library.
---

# AndromedaQuery V1.0.0

## AndromedaQuery

All of the ADOs can call the base query messages found in the AndromedaMsg.&#x20;

{% hint style="danger" %}
AMP ADOs are the only ADOs that do not implement the AndromedaQuery base queries.
{% endhint %}

```rust
pub enum AndromedaQuery {
    #[returns(self::ownership::ContractOwnerResponse)]
    Owner {},
    #[returns(self::ownership::ContractPotentialOwnerResponse)]
    OwnershipRequest {},
    #[returns(self::ado_type::TypeResponse)]
    Type {},
    #[returns(self::kernel_address::KernelAddressResponse)]
    KernelAddress {},
    #[returns(self::ownership::PublisherResponse)]
    OriginalPublisher {},
    #[returns(self::block_height::BlockHeightResponse)]
    BlockHeightUponCreation {},
    #[returns(self::version::VersionResponse)]
    Version {},
    #[returns(Option<::cosmwasm_std::Addr>)]
    AppContract {},
    #[returns(::cosmwasm_std::BalanceResponse)]
    Balance { address: AndrAddr },
    #[returns(Vec<self::permissioning::PermissionInfo>)]
    Permissions {
        actor: AndrAddr,
        limit: Option<u32>,
        start_after: Option<String>,
    },
    #[returns(Vec<self::permissioning::PermissionedActionsResponse>)]
    PermissionedActions {},
}
```

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(self::ownership::ContractOwnerResponse)]
    Owner{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "owner":{}
  }
}
```
{% endtab %}
{% endtabs %}

#### ContractOwnerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ContractOwnerResponse {
    pub owner: String
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

### OwnershipRequest

Returns the owner that has a request to take ownership of the ADO.&#x20;

{% hint style="info" %}
An OwnershipRequest is present when [UpdateOwner](andromedamsg-v1.0.0.md#updateowner) is called.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>#[returns(self::ownership::ContractPotentialOwnerResponse)]
</strong>OwnershipRequest {},
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"ownership_request":{}
}
```
{% endtab %}
{% endtabs %}

#### ContractPotentialOwnerResponse

A struct that returns the address that has the ownership offer for the ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
{
pub struct ContractPotentialOwnerResponse {
    pub potential_owner: Option<Addr>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"potential_owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name              | Type          | Description                                             |
| ----------------- | ------------- | ------------------------------------------------------- |
| `potential_owner` | Option\<Addr> | The address that has been offered ownership of the ADO. |

### Type

Queries the ADO type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(self::ado_type::TypeResponse)]
</strong>    Type {}
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
<strong>"type":{}
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

#### TypeResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TypeResponse {
    pub ado_type: String,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_type":"auction"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `ado_type` | String | The type of the ado. |

### KernelAddress

Queries the kernel address of the chain the ADO is deployed on.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(self::kernel_address::KernelAddressResponse)]
    KernelAddress {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":{}
}
```
{% endtab %}
{% endtabs %}

Returns a String containing the contract address of the Kernel.

### BlockHeightUponCreation

Queries the block height when the ADO was created.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
      #[returns(self::block_height::BlockHeightResponse)]
      BlockHeightUponCreation {},
      }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"block_height_upon_creation":{}
}
```
{% endtab %}
{% endtabs %}

#### BlockHeightResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct BlockHeightResponse {
    pub block_height: u64,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"block_height": 2000134
}
```
{% endtab %}
{% endtabs %}

| Name           | Type | Description                                          |
| -------------- | ---- | ---------------------------------------------------- |
| `block_height` | u64  | The block height at the time of creation of the ADO. |

### Version

Queries the version of the ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
     #[returns(self::version::VersionResponse)]
     Version {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version":{}
}
```
{% endtab %}
{% endtabs %}

#### VersionResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct VersionResponse {
    pub version: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version": "0.1.0"
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Descripton               |
| --------- | ------ | ------------------------ |
| `version` | String | The version of the ADO.  |

###

### AppContract

Returns the address of the App ADO that instantiated the ADO.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum AndromedaQuery {
</strong> #[returns(self::app_contract::AppContractResponse)]
 AppContract {}
 }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"app_contract":{}
}
```
{% endtab %}
{% endtabs %}

#### AppContractResponse

The struct containing the address of the App ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct AppContractResponse {
    pub app_contract: Addr,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"app_contract":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name           | Type | Description                                        |
| -------------- | ---- | -------------------------------------------------- |
| `app_contract` | Addr | The address of the App  that instantiated the ADO. |

### OriginalPublisher

Queries the orginal address that published/instantiated the ADO.

{% tabs %}
{% tab title="Rust " %}
```rust
pub enum AndromedaQuery {
    #[returns(self::ownership::PublisherResponse)]
    OriginalPublisher {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"original_publisher":{}
}
```
{% endtab %}
{% endtabs %}

#### PublisherResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PublisherResponse {
    pub original_publisher: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"original_publisher":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type   | Description                                               |
| -------------------- | ------ | --------------------------------------------------------- |
| `original_publisher` | String | The original address that instantiated/published the ADO. |

### Permisions

Queries the permissions set for the specified actor address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(Vec<self::permissioning::PermissionInfo>)]
    Permissions {
        actor: AndrAddr,
        limit: Option<u32>,
        start_after: Option<String>,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissions":{
    "actor":"andr1...",
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                    | Description                                                                                             |
| ------------- | --------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `actor`       | [AndrAddr](../common-types.md#andraddr) | The address we are checking permissions for.                                                            |
| `limit`       | Option\<u32>                            | Optional limit to the number of permissions returned. Defaults to 25 and can be set to a maximum of 50. |
| `start_after` | Option\<String>                         | Optional permission to start after. Used for pagination.                                                |

#### PermissionInfo

Returns a vector of the PermissionInfo struct containing all the permissions for the actor.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PermissionInfo {
    pub permission: Permission,
    pub action: String,
    pub actor: String,
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                              | Description                                       |
| ------------ | ------------------------------------------------- | ------------------------------------------------- |
| `permission` | [Permission](andromedaquery-v1.0.0.md#permission) | The permission that the actor was given.          |
| `action`     | String                                            | The action or message that the permission is for. |
| `actor`      | String                                            | The address that has these permissions.           |

### PermissionedActions

Queries the actions or execute messages that are permissioned in the ADO.&#x20;

{% hint style="warning" %}
The actions that are permissioned are the ones that have [PermissionAction](andromedaquery-v1.0.0.md#permissionaction) called on them.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(Vec<self::permissioning::PermissionedActionsResponse>)]
    PermissionedActions {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"permissioned_actions":{}
}
```
{% endtab %}
{% endtabs %}

Returns a Vector of String containing the actions that are permissioned.&#x20;
