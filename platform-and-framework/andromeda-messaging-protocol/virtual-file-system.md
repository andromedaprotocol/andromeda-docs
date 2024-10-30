# Virtual File System

{% hint style="danger" %}
Username registration is currently disabled on mainnet.
{% endhint %}

## Introduction

The **Virtual File System** (VFS) is a part of the Andromeda Messaging System (AMP) which was heavily inspired by the linux file system. Users can register their address to a username. They can also register ADOs to paths. These paths can then be used and referenced in our ADO systems.

When an [Andromeda App](../../andromeda-digital-objects/app-v1.0.1.md) is made, it will register all paths for its child components and also register itself as a child of the instantiating address. Each component under the App is registered by its name, and the App itself is registered under its assigned name.

In addition to paths, [symbolic links](virtual-file-system.md#addsymlink) that point to a path can be created by users.

{% hint style="warning" %}
All paths and usernames are resolved by the VFS in lowercase. For example, the username "USER" and "user" are considered to be the same. Same applies for paths, "`/home/USERNAME"` and  "`/home/username"` are the same.&#x20;
{% endhint %}

#### **Example:**

A user does the following:

1. Registers their address as user1 by calling [RegisterUser](virtual-file-system.md#registeruser)
2. The user then creates an App and calls it "app-1" which has 2 compnents named "comp-1" and "comp-2"
3. The App will automatically register its components under their assigned names

{% hint style="danger" %}
When you register a username, it will registered in the "home" directory. This can also be represented by the "\~" symbol or the "." symbol.

In the examples below, /home can be replaced by \~ in the paths. For example,  /home/user1 and \~/user1 and ./user1 are considered the same.
{% endhint %}

The following paths are now registered:

* /home/user1 will resolve the the user's address
* /home/user1/app-1 is the path to get the address of the app contract
* /home/user/app-1/comp-1 is the path to get the address of component 1 in the app
* /home/user/app-1/comp-2 is the path to get the address of component 2 in the app

#### When Can we Use a VFS Path

When you go through our ADOs, you will notice an **AndrAddr** struct used very frequently for specifying addresses, whether be user addresses or ADO addresses. The use of this struct signifies that the address can be referenced by either the VFS path or the user/contract address. More on the **AndrAddr** struct can be found[ here](../common-types.md#andraddr).

#### VFS Paths in Apps

Local references can be used in case of Apps. These would include the component addresses and can be referenced using " **./\<component-name>**" as a valid path.&#x20;

For example if I want to create an app with two components named "token" and "auction", and in the auction I want to reference the token address, I can use **./token** to do so. Another example here would be a sending an NFT from a CW721 to an auction. Instead of specifying the auction contract address as the recipient, you can use the local reference **./auction** to do so as long as the components belong to the same App.

**Ado\_type:** vfs

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type            | Description                                                                                                        |
| ---------------- | --------------- | ------------------------------------------------------------------------------------------------------------------ |
| `kernel_address` | String          | Address of the Kernel contract on chain. Can be found in our [deployed contracts](<../deployed-contracts (1).md>). |
| `owner`          | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaultes to the sender if not specified.       |

### ExecuteMsg

### AddPath

Registers a component to the path.&#x20;

{% hint style="warning" %}
Only accepts alphanumeric charaters.

If user1 registers ado1, then the path created would be /home/user1/ado1.

Registering a path that is already found will overwrite the old path.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg {    
</strong>    AddPath {
        name: String,
        address: Addr,
        parent_address:Option&#x3C;AndrAddr> 
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_path":{
    "name":"ado1",
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                            | Description                                                                                                                   |
| ---------------- | ----------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `name`           | String                                          | The name of the component in the path.                                                                                        |
| `address`        | Addr                                            | The address of the component.                                                                                                 |
| `parent_address` | Option<[AndrAddr](../common-types.md#andraddr)> | Optional address to specify as the parent for the component. If not specified, then the sender will be the parent by default. |

### AddSymlink

Creates a symbolic link that points to a specific path.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
AddSymlink {
        name: String,
        symlink: AndrAddr,
        parent_address: Option<AndrAddr>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_symlink":{
    "name":"my-link",
    "symlink":"/home/user2/app1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                            | Description                                |
| ---------------- | ----------------------------------------------- | ------------------------------------------ |
| `name`           | String                                          | The name of the symlink.                   |
| `symlink`        | AndrAddr                                        | The path to point to.                      |
| `parent_address` | Option<[AndrAddr](../common-types.md#andraddr)> | The address to register the symlink under. |

In the **JSON** example above, if the username of the sender (parent address) is user1, then the symlink /home/user1/my-link would resolve to the path /home/user2/app1.

### RegisterUser

Registers a username to an address.&#x20;

{% hint style="warning" %}
* You can only register a username for your address (sender).
* Only accepts alphanumeric charaters.
* Usernames are unique, meaning no two users can have the same username.
* If `address` is provided sender must be Kernel.
* Username cannot exceed 40 characters.
* This message is currently disabled on mainnet.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
    RegisterUser {
        username: String,
        address: Option<Addr>,
        }
    }   
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"register_user":{
    "username":"user1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type          | Description                                                             |
| ---------- | ------------- | ----------------------------------------------------------------------- |
| `username` | String        | The username to register.                                               |
| `address`  | Option\<Addr> | The address to register the username to (Only available to the kernel). |

### RegisterUserCrossChain

Registers the username of the sender on another chain.&#x20;

Registerning a username on another chain needs to be done through the Andromeda chain first. Once the username is registered on the second chain, it is possible to register it to a third chain through Andromeda or the second chain.

For example, if I want to register my username on Juno and then Terra, I would need to call **RegisterUserCrossChain** message from the Andromeda chain **FIRST** specifying the `chain` field as juno. Once registered on Juno, I can call the message from either Andromeda or Juno to register it on Terra.&#x20;

{% hint style="warning" %}
This message is currently disabled on mainnet.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  RegisterUserCrossChain {
        chain: String,
        address: String,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```
// Some code
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                                       |
| --------- | ------ | ----------------------------------------------------------------- |
| `chain`   | String | The name of the chain you want to register the username on.       |
| `address` | String | The address on the specified `chain` to register the username to. |

### RegisterLibrary

Registers a library address under a library name. Libraries will be set up by Andromeda, and will contain a group of paths that resolve to addresses that can be used by users to create Apps. Users can use the [SubDir](virtual-file-system.md#subdir) query to get all the directories that are saved under the library name.

{% hint style="warning" %}
This message cannot be called by a user.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum Execute {
 RegisterLibrary {
        lib_name: String,
        lib_address: Addr,
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type   | Description                 |
| ------------- | ------ | --------------------------- |
| `lib_name`    | String | The name of the library.    |
| `lib_address` | Addr   | The address of the library. |

### AddChild

Registers the child's path relative to the parent.

{% hint style="warning" %}
Cannot be called by a user.

When Andromeda Apps are instantiated, it is automatically called, registering the App as a child to the sender's username and each of the components as children of the App itself similar to what we saw in the [example above.](virtual-file-system.md#example)
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
        AddParentPath {
        name: String,
        parent_address: AndrAddr,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_parent_path":{
    "name": "ado7",
    "parent_address":"/user1/app1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                    | Description                    |
| ---------------- | --------------------------------------- | ------------------------------ |
| `name`           | String                                  | The name to assign.            |
| `parent_address` | [AndrAddr](../common-types.md#andraddr) | The address of the parent ADO. |

### Ownership

The set of ownerhsip messages. These messages are the same as the ones found in the [ADO base section](../ado-base/andromedamsg-v1.0.0.md#ownership).

## QueryMsg

### ResolvePath

Gets the address of the specified path.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Addr)]
    ResolvePath { path: AndrAddr },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"resolve_path":{
    "path":"~/user1/app1/ado2"
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type                                    | Description                      |
| ------ | --------------------------------------- | -------------------------------- |
| `path` | [AndrAddr](../common-types.md#andraddr) | The path to get the address for. |

Returns the address of of the path.  In the above JSON example, the address of ado2 would be returned.

### SubDir

Gets all the paths under the specified directory. Min and max can be used for pagination.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  #[returns(Vec<PathDetails>)]
    SubDir {
        path: AndrAddr,
        min: Option<SubDirBound>,
        max: Option<SubDirBound>,
        limit: Option<u32>,
    }
 }

 pub struct SubDirBound {
    address: Addr,
    name: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"sub_dir":{
    "path":"~/user1/app1",
    "limit": 68
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                    | Description                                                                                    |
| ------- | --------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `path`  | [AndrAddr](../common-types.md#andraddr) | The path to get all sub directories for.                                                       |
| `min`   | Option\<SubDirBound>                    | The key to start from. Used for pagination.                                                    |
| `max`   | Option\<SubDirBound>                    | The maximum key to return. Used for pagination.                                                |
| `limit` | Option\<u32>                            | The number of paths to be returned. Defaults to 50 if not set. Can be set to a maximum of 100. |

#### PathDetails

Returns the path name and corresponding address for each subdirectory path.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct PathDetails {
    name: String,
    address: Addr,
}
```
{% endtab %}
{% endtabs %}

### Paths

Gets all the paths that resolve to the specified address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Vec<String>)]
    Paths { addr: Addr },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"paths":{
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type | Description                       |
| ------ | ---- | --------------------------------- |
| `addr` | Addr | The address to get the paths for. |

### GetUsername

Gets the registered username of the specified address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(String)]
    GetUsername { address: Addr },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_username":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type | Description                          |
| --------- | ---- | ------------------------------------ |
| `address` | Addr | The address to get the username for. |

### GetLibrary

Gets the library name of the specified library address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(String)]
    GetLibrary { address: Addr },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_library":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type | Description                                      |
| --------- | ---- | ------------------------------------------------ |
| `address` | Addr | The address of the library to get the name for.  |

### ResolveSymlink

Returns the path that the specified symlink points to.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  #[returns(AndrAddr)]
   ResolveSymlink { path:AndrAddr },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"resolve_symlink":{
    "path":"~/user1/symlink1"
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type                                    | Description                         |
| ------ | --------------------------------------- | ----------------------------------- |
| `path` | [AndrAddr](../common-types.md#andraddr) | The path of the symlink to resolve. |

### Version

Queries the version of the ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
     #[returns(VersionResponse)]
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

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
    #[returns(ContractOwnerResponse)]
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

### Type

Queries the ADO type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(TypeResponse)]
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
    #[returns(KernelAddressResponse)]
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
