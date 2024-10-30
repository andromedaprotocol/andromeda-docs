# App V1.0.1

## Introduction

The **App** ADO is a smart contract that is used to bundle up ADOs that will be interacting with each other into  what we call an  "App". It also offers a naming system that allows the contracts to reference each other by their assigned names rather than contract addresses.&#x20;

An ADO in the App is called an AppComponent. Every App would be composed of many of these components (up to 50). Each component is assigned a name which can be used by other components to reference each other. The App ADO allows us to instantiate all of these components in one go.&#x20;

{% hint style="warning" %}
To reference a component from the another component when instantiating an App, you would need to speciy "./\<component-name>. You will see examples of this in our [Andromeda Apps](broken-reference) section.
{% endhint %}

At instantiation, we specify the address of the [Kernel ADO](../platform-and-framework/andromeda-messaging-protocol/kernel.md) . This Kernel will have a reference to the [ADODB](../platform-and-framework/ado-base/) which has the code Ids of all the Andromeda ADOs saved. The Kernel ADO for each chain is already deployed and the addresses can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>) section. You can learn to deploy your first app [here](../andromeda-apps/crowdfunding-app.md).

Our Apps support cross-chain components or ADOs, meaning an App can contain several ADOs each located on a different chain. This is specified by using the `CrossChain` component type.

{% hint style="warning" %}
The App registers all its components in the [Virtual File System](../platform-and-framework/andromeda-messaging-protocol/virtual-file-system.md) upon instantiation and assigns to them the names specified by the user upon instantiation of the App.

Crosschain Apps are currently disabled.
{% endhint %}

**Ado\_type**: app-contract

## InstantiateMsg

{% hint style="warning" %}
The maximum number of app components is 50.

You cannot have 2 Apps with the same name instantiated by the same address.

The instantiator address is given ownership over all the components of the App at instantiation.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub app_components: Vec<AppComponent>,
    pub name: String,
    pub chain_info: Option<Vec<ChainInfo>>,
    pub kernel_address:String,
    pub owner:Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"app_components":[
     {
       "name":"fundsplitter",
       "ado_type":"splitter",
       "component_type":{
       "new":"eyJtaW50ZXIiOiAianVubzE3OX..."
       }
     },
   {
   ...
   }
 ],
 "name":"some_app",
 "kernel_address":"andr1...",
 "owner":"andr1..."
 }
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="264.3333333333333">Name </th><th width="247.18815855494233">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>app_components</code></td><td>Vec&#x3C;<a href="app-v1.0.1.md#appcomponent">AppComponent</a>></td><td>The vector of AppComponent containing all the ADOs of the app.</td></tr><tr><td><code>name</code></td><td>String</td><td>The name of the app.</td></tr><tr><td><code>chain_info</code></td><td>Option&#x3C;Vec&#x3C;<a href="app-v1.0.1.md#chaininfo">ChainInfo</a>>></td><td>A vector containing the chain information describing the chains that will be used in the App. To be able to deploy a CrossChain component, the chain_info for that chain need to be specified.</td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts (1).md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified. </td></tr></tbody></table>

### AppComponent

The ADO to be a part of the App.&#x20;

{% hint style="warning" %}
The `name` field is case sensitive and needs to be unique for each component.

Component name characters should be alphanumeric.

Component name cannot be a single dot "."
{% endhint %}

```rust
pub struct AppComponent {
    pub name: String,
    pub ado_type: String,
    pub component_type: ComponentType,
}
```

| Name             | Type                                        | Description                                                                               |
| ---------------- | ------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `name`           | String.                                     | The name of the ADO component. The names can be used later on to reference the coponent.  |
| `ado_type`       | String                                      | The type of the ADO.                                                                      |
| `component_type` | [ComponentType](app-v1.0.1.md#componenttye) | Specifies the type of component. The components types are discussed below.                |

#### ComponentType

An enum containing the different component types that can be instantiated.

{% hint style="warning" %}
In order to create a CrossChain component, `chain_info`field (Found in instantiation message of the App ADO) needs be specified for the chain to deploy the component on. For example, if I want to create a NFT component on Stargaze from an App on the Andromeda chain, I need to specify the [chain info](app-v1.0.1.md#chaininfo) for the stargaze chain for it to be successfull.

CrossChain Components are currently disabled.
{% endhint %}

```rust
pub enum ComponentType {
    New(Binary),
    Symlink(AndrAddr),
    CrossChain(CrossChainComponent),
}
```

There are three types of components:

* **New:** Provide a base64 encoded binary of the instantiation message of the component to add.
* **Symlink:** Provide a valid Symlink that resolves to the address of the component to add.
* **CrossChain:** Provide a base64 encoded binary of the instantiation message of the component to add as well as the chain. The component will be instantiated on the specified chain. This option is currently disabled.

#### CrossChainComponent

{% hint style="warning" %}
The `instantiate_msg` should be base64 encoded and not raw binary.
{% endhint %}

```rust
pub struct CrossChainComponent {
    pub instantiate_msg: Binary,
    pub chain: String,
}
```

| Name              | Type   | Description                                                                 |
| ----------------- | ------ | --------------------------------------------------------------------------- |
| `instantiate_msg` | Binary | base64 binary representation of the instantiation message of the comopnent. |
| `chain`           | String | The chain to create the component on.                                       |

### ChainInfo

Information on the chains that will be part of the App. Need to be specified to be able to use CrossChain components.

```rust
#[cw_serde]
pub struct ChainInfo {
    pub chain_name: String,
    pub owner: String,
}
```

| Name         | Type   | Description                                                                             |
| ------------ | ------ | --------------------------------------------------------------------------------------- |
| `chain_name` | String | The name of the chain.                                                                  |
| `owner`      | String | The address of the owner of the components that will be created on the specified chain. |

## ExecuteMsg

### AddAppComponent

Adds an ADO component to the app.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
AddAppComponent{
 component: AppComponent,
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_app_component":{
 "component":{
       "name":"crowdfund",
       "ado_type":"crowdfund",
       "component_type":{
       "new":"eyJ0b2tlbl9hZGRyZXNzIjp7ImlkZW50aWZpZXIiOiJqdW5vMS4uLiJ9InByaW1pdGl2ZV9hZGRyZXNzIjoianVub..."
       }
   }
}         
```
{% endtab %}
{% endtabs %}

| Name        | Type                                       | Description                          |
| ----------- | ------------------------------------------ | ------------------------------------ |
| `component` | [AppComponent](app-v1.0.1.md#appcomponent) | The ADO component to add to the app. |

### ClaimOwnership

Sends an ownership request to the specified `new_owner` for a component owned by the App.&#x20;

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   ClaimOwnership { 
      name: Option<String>,
      new_owner:Option<Addr>
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_ownership":{
  "name":"mycomponent",
  "new_owner":"andr1..."
  }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type            | Description                                                                                     |
| ----------- | --------------- | ----------------------------------------------------------------------------------------------- |
| `name`      | Option\<String> | Optional name for the component to claim. If not set, will claim all ADO components in the app. |
| `new_owner` | Option\<Addr>   | Optional address to get the  ownership request. Defaults to the sender if not specified.        |

### ProxyMessage

Sends a message to the ADO with the specified `name`. This is used in the case the app contract has specific operation privileges (Is the owner)  over a component.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
  pub enum ExecuteMsg{
  ProxyMessage { 
    name: String,
    msg: Binary 
    }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"proxy_message":{
 "name":"componentname",
 "msg":"eyJtaW50Ijp7InRva2VuX2lkIjo..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                                                                                     |
| ------ | ------ | ----------------------------------------------------------------------------------------------- |
| `name` | String | The name of the ADO to execute on.                                                              |
| `msg`  | Binary | The msg to excecute. This is a base64 encoded binary of the JSON representation of the message. |

### UpdateAddress

Sets a new contract address for the ADO with the specified `name`.

{% hint style="warning" %}
Only available to the contract owner or the ADO component with the specified name.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  UpdateAddress {
     name: String,
     addr: String
   }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_address":{
  "name":"roayalties",
  "addr":"andr1..."
  }
 }

```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                                                                                                             |
| ------ | ------ | ----------------------------------------------------------------------------------------------------------------------- |
| `name` | String | The name of the ADO component to update the address for. This needs to be the name of one of the components in the App. |
| `addr` | String | The new contract address to be associated with the specified name.                                                      |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### GetAddress

Queries the contract address of a component name.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
 #[returns(String)]
 GetAddress { name: String }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_address":{
  "name":"componentname"
   }
 }
```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                                                |
| ------ | ------ | ---------------------------------------------------------- |
| `name` | String | The name of the component to get the contract address for. |

Returns a `String` of the component address.

### GetAddressesWithNames

Queries all the components returning the name along with the address for each.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Vec<ComponentAddress>)]
    GetAddressesWithNames {}
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_addresses_with_names":{}
}
```
{% endtab %}
{% endtabs %}

#### ComponentAddress

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ComponentAddress {
    pub name: String,
    pub address: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"name":"token",
"address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

### GetComponents

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
 #[returns(Vec<AppComponent>)]
 GetComponents {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_components":{}
 }
```
{% endtab %}
{% endtabs %}

Returns a `Vec<`[`AppComponent`](app-v1.0.1.md#appcomponent)`>` which contains all the components of the app.

### Config

Queries the configuration of the app.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
    #[returns(ConfigResponse)]
    Config {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"config":{}
 }
```
{% endtab %}
{% endtabs %}

#### ConfigResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ConfigResponse {
    pub owner: String,
    pub name: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"name":"myapp",
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

### ComponentExists

Checks if a component with the specified `name` exists.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(bool)]
    ComponentExists { name: String },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"component_exists":{
    "name":"mycomponent"
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                         |
| ------ | ------ | ----------------------------------- |
| `name` | String | The name of the component to check. |

Returns a bool response.

### &#x20;Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
