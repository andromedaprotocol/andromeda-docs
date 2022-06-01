# App

## Introduction

The **App** ADO is a smart contract that is used to bundle up contracts that will be interacting with each other into  what we call an  "App" and would provide a naming system to allow these contracts to reference each other using these names instead of contract addresses. Essentially, an App would include several contracts that interact to complete a desired goal for the project being built.&#x20;

A contract in the App is called a AppComponent. Every app would be composed of many of these components (up to 50). Each component is assigned a name which can be used by other components to reference each other.

The steps needed to build a full app are the following:

1. Upload the contracts and get the code IDs.
2. Instantiate a factory contract and save the code IDs of the contracts in it.
3. Instantiate a primitive contract and save the factory address as a value under the name "factory".
4. Instantiate the app contract and attach the primitive to it (`primitive_contract` field in instantiation)

**Ado\_type**: app

## InstantiateMsg

{% hint style="warning" %}
The maximum number of app components is 50.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub operators: Vec<String>,
    pub app: Vec<AppComponent>,
    pub name: String,
    pub primitive_contract: String
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"operators":["juno1...","juno1...","juno1...",...],
"app":[
     {
       "name":"fundsplitter",
       "ado_type":"splitter",
       "instantiate_msg":{
       ...
       }
     },
   {
   ...
   }
 ],
 "name":"some_app",
 "primitive_contract":"juno1..."
 }
       
     

```
{% endtab %}
{% endtabs %}

| Name                 | Type                                         | Description                                                            |
| -------------------- | -------------------------------------------- | ---------------------------------------------------------------------- |
| `operators`          | Vec\<String>                                 | The operators of the contract.                                         |
| `App`                | Vec<[AppComponent](app.md#missioncomponent)> | The vector of AppComponent containing all the ADOs of the app.         |
| `name`               | String                                       | The name of the app.                                                   |
| `primitive_contract` | String                                       | The address of the `primitve` contract used to supply data to the app. |

### AppComponent

The ADO to be a part of the App.&#x20;

{% hint style="warning" %}
The `instantiate_msg` should be base64 encoded and not raw binary.
{% endhint %}

```rust
pub struct AppComponent {
    pub name: String,
    pub ado_type: String,
    pub instantiate_msg: Binary,
}
```

| Name              | Type    | Description                                   |
| ----------------- | ------- | --------------------------------------------- |
| `name`            | String. | The name of the ADO component.                |
| `ado_type`        | String  | The type of the ADO.                          |
| `instantiate_msg` | Binary  | The instantiate message of the ADO component. |

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
       "name":"fundsplitter",
       "ado_type":"splitter",
       "instantiate_msg":{
       ...
       }
   }
}
            
```
{% endtab %}
{% endtabs %}

| Name        | Type                                    | Description                          |
| ----------- | --------------------------------------- | ------------------------------------ |
| `component` | [AppComponent](app.md#missioncomponent) | The ADO component to add to the app. |

### ClaimOwnership

Gives ownership of a component to the owner address instead of the app contract.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
   ClaimOwnership { 
   name: Option<String> 
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"claim_ownership":{
  "name":"mycomponent"
  }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type            | Description                                                                                     |
| ------ | --------------- | ----------------------------------------------------------------------------------------------- |
| `name` | Option\<String> | Optional name for the component to claim. If not set, will claim all ADO components in the app. |

### ProxyMessage

Sends a message to the ADO with the specified `name`. This is used in the case the app contract has specific operation privileges over a component.

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
 "msg":{
   "update":{
     ...
        }
   
      }
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                           |
| ------ | ------ | ------------------------------------- |
| `name` | String | The name of the ADO to execute on.    |
| `msg`  | Binary | The msg to excecute (Base 64 binary). |

### UpdateAddress

Sets a new contract address for the ADO with the specified `name`.

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
 "name":"componentname",
 "addr":"juno1..."
  }
 }

```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                            |
| ------ | ------ | -------------------------------------- |
| `name` | String | The name of the ADO.                   |
| `addr` | String | The new contract address for the ADO.  |

### AndrReceive

Check [AndrReceive](../../ado\_base/andrreceive-andrquery.md).

## QueryMsg

### GetAddress

Queries the contract address of a component name.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
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

### GetAddresses

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
   GetAddresses {}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_addresses":{}
 }
```
{% endtab %}
{% endtabs %}

Returns a `Vec<ComponentAddress>` containing the name and address of each component.

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
"name":"componentname",
"address":"juno1..."
}
```
{% endtab %}
{% endtabs %}

### GetComponents

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
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

Returns a `Vec<`[`AppComponent`](app.md#missioncomponent)`>` which contains all the components of the app.

### Config

Queries the configuration of the app.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
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
"owner":"juno1..."
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

### AndrQuery

```rust
pub enum QueryMsg {
    AndrQuery(AndromedaQuery),
}
```

If the [`AndromedaQuery`](../../ado\_base/andrreceive-andrquery.md#andromedaquery) is of type `Get` , the contract will query address of the specified name (data) . If no data is supplied in the Get, an error will occur.

```rust
fn handle_andromeda_query(
    deps: Deps,
    env: Env,-
    msg: AndromedaQuery,
) -> Result<Binary, ContractError> {
    match msg {
        AndromedaQuery::Get(data) => match data {
            None => Err(ContractError::ParsingError {
                err: String::from("No data passed with AndrGet query"),
            }),
            Some(_) => {
                //Default to get address for given ADO name
                let name: String = parse_message(&data)?;
                encode_binary(&query_component_address(deps, name)?)
            }
        },
        _ => ADOContract::default().query(deps, env, msg, query),
    }
}
```

Check [AndrQuery](../../ado\_base/andrreceive-andrquery.md#andrquery) for the rest of the default queries.
