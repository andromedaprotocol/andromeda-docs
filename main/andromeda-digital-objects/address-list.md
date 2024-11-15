# Address List

The address list ADO is a smart contract that facilitates setting up [permissions](../platform-and-framework/ado-base/andromedamsg.md#permissioning) for ADOs. Instead of setting up the permission on the ADO itself, the owner can reference this ADO instead.

Permissioning allows ADO owners to give/restrict access to addresses to execute messages on their ADOs.

**ADO\_type: address-list**

**Version: 2.0.2-beta.1**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub actor_permission: Option<ActorPermission>,
    pub kernel_address: String,
    pub owner: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"actor_permission":{
    "actor":"andr1...",
    "permission":{
        "whitelisted":null
        }
    },
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name               | Type                                                         | Description                                                                                                                           |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| `actor_permission` | Option<[ActionPermission](address-list.md#actionpermission)> | Optional permission to set at instantiation.                                                                                          |
| `kernel_address`   | String                                                       | Contract address of the kernel contract to be used for AMP messaging. Kernel contract address can be found in our deployed contracts. |
| `owner`            | Option\<String>                                              | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                           |

#### ActionPermission

```rust
pub struct ActorPermission {
    pub actor: Addr,
    pub permission: Permission,
}
```

| Name         | Type                                     | Description                                    |
| ------------ | ---------------------------------------- | ---------------------------------------------- |
| `actor`      | Addr                                     | The address to apply the permission on.        |
| `permission` | [Permission](address-list.md#permission) | The type of permission to assign to the actor. |

#### Permission

An enum to represent a user's permission for an action:

{% hint style="warning" %}
Expiration defaults to `Never` if not provided.
{% endhint %}

```rust
pub enum Permission {
    Blacklisted(Option<Expiry>),
    Limited {
        expiration: Option<Expiry>,
        uses: u32,
    },
    Whitelisted(Option<Expiry>),
}
```

* **Blacklisted:** The user cannot perform the action until after the provided expiration.
* **Limited:** The user can perform the action while uses are remaining and before the provided expiration.
* **Whitelisted:** The user can perform the action until the provided expiration.

## ExecuteMsg

### AddActorPermission

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

Add permissioning to the specified list of actors (Addresses).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
PermissionActors {
        actors: Vec<AndrAddr>,
        permission: LocalPermission,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"actor_permission":{
    "actors":["andr1...","andr1...",...],
    "permission":{
        "blacklisted":null
        }
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type                                                                | Description                                       |
| ------------ | ------------------------------------------------------------------- | ------------------------------------------------- |
| `actors`     | Vec<[AndrAddr](../platform-and-framework/common-types.md#andraddr)> | The list of addresses to apply the permission to. |
| `permission` | [Permission](address-list.md#permission)                            | The type of permission to assign to the actor.    |

### RemoveActorPermission

{% hint style="warning" %}
Only available to the ADO owner.
{% endhint %}

Removes previously set permission on the specified actors.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  RemovePermissions {
        actors: Vec<AndrAddr>
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"remove_actor_permission":{
    "actors":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name     | Type                                                                | Description                             |
| -------- | ------------------------------------------------------------------- | --------------------------------------- |
| `actors` | Vec<[AndrAddr](../platform-and-framework/common-types.md#andraddr)> | The address to remove permissions from. |

### Base Executes

The rest of the execute messages can be found in the [ADO Base](../platform-and-framework/ado-base/andromedamsg.md) section.

## QueryMsg

### IncludesActor

Checks if the specified actor has any permissions applied.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(IncludesActorResponse)]
    IncludesActor { actor: Addr },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"includes_actor":{
    "actor":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type | Description                           |
| ------- | ---- | ------------------------------------- |
| `actor` | Addr | The address to check permissions for. |

Returns true if the address has any permissions and false otherwise.

### ActorPermission

Queries the permission applied on the specified actor.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
   #[returns(ActorPermissionResponse)]
    ActorPermission { actor: Addr },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"actor_permission":{
    "actor":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type | Description                         |
| ------- | ---- | ----------------------------------- |
| `actor` | Addr | The address to get permissions for. |

Returns a [Permission](address-list.md#permission) struct with the type of permission.

### Base Queries

The rest of the query messages can be found in the [ADO Base ](../platform-and-framework/ado-base/andromedaquery.md)section.
