---
description: >-
  Each mission is made up of mission components (ADO contracts) which will be
  provided a name. This name will be linked to the ADO contract address. It
  allows then to query these addresses by name.
---

# Mission

### MissionComponent

The ADO to be a part of the mission.

```rust
pub struct MissionComponent {
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

## InstantiateMsg

{% hint style="info" %}
The maximum number of mission components is 50.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub operators: Vec<String>,
    pub mission: Vec<MissionComponent>,
    pub xfer_ado_ownership: bool,
    pub name: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"operators":["terra1...","terra1...","terra1...",....],
"mission":[
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
 "xfer_ado_ownership": true,
 "name":"some_mission"
 }
       
     

```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                 | Description                                                            |
| -------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------- |
| `operators`          | Vec\<String>                                         | The operators of the contract.                                         |
| `mission`            | Vec<[MissionComponent](mission.md#missioncomponent)> | The vector of MissionComponent containing all the ADOs of the mission. |
| `xfer_ado_ownership` | bool                                                 | A flag to whether to keep the mission contract as the owner            |
| `name`               | String                                               | The name of the mission.                                               |

## ExecuteMsg

### AddMissionComponent

Adds an ADO component to the mission.

{% hint style="info" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
AddMissionComponent{
 component: MissionComponent,
   }
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"add_mission_component":{
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

| Name      | Type                                            | Description                              |
| --------- | ----------------------------------------------- | ---------------------------------------- |
| component | [MissionComponent](mission.md#missioncomponent) | The ADO component to add to the mission. |

### ClaimOwnership

Gives ownership of a component owner address instead of the mission contract.

{% hint style="info" %}
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
  "name":"mycomponent",
  }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type            | Description                                                                                         |
| ------ | --------------- | --------------------------------------------------------------------------------------------------- |
| `name` | Option\<String> | Optional name for the component to claim. If not set, will claim all ADO components in the mission. |

### ProxyMessage

Sends a message to the ADO with the specified `name`. This is used in the case the mission contract has specific operation privileges over a component.

{% hint style="info" %}
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

| Name   | Type   | Description                        |
| ------ | ------ | ---------------------------------- |
| `name` | String | The name of the ADO to execute on. |
| `msg`  | Binary | The msg to excecute.               |

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
 "addr":"terra1...",
  }
 }

```
{% endtab %}
{% endtabs %}

| Name   | Type   | Description                            |
| ------ | ------ | -------------------------------------- |
| `name` | String | The name of the ADO.                   |
| `addr` | String | The new contract address for the ADO.  |

### UpdateOwner/UpdateOperators

Check [AndrReceive](../andrreceive-andrquery.md).

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

Returns the contract address as a string.

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

Returns a `Vec<Addr>` containing the different contract addresses of the components.

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

Returns a `Vec<`[`MissionComponent`](mission.md#missioncomponent)`>` which contains all the components of the mission.

### Config

Queries the configuration of the mission.

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
"name":"mymission",
"owner":"terra1...",
}
```
{% endtab %}
{% endtabs %}

### Owner/Operators/IsOperator

Check [AndrQuery](../andrreceive-andrquery.md).