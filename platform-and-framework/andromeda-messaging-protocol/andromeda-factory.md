# ADO Database

## Introduction

The **Database** ADO (ADODB) is a smart contract that is primarily used to store the code Ids for  Andromeda ADOs. These code Ids are used to instantiate these ADOs in Andromeda [Apps](../../andromeda-digital-objects/app-v1.0.1.md). The code Ids are stored within a key value pair where the key is the ADO type and the value is the ADO code Id.

The ADODB is also responsible for managing the [economic engine](economics-engine.md) of the Andromeda ecosystem, allowing ADO publishers  to set custom fees to be paid when interacting with one of their published ADOs. These fees can be set as native or a CW20 token.

Currently all published ADOs have been done by and through the Andromeda team. This does not mean that ADOs developed by Andromeda are the only ones that can be published to the ADODB. On the contrary, we encourage developers to create new and exciting ADOs to be published that can be submitted to our team to be reviewed. If the quality of the ADO is found to be to standard, the developer is given permission to publish it into the existing library of ADOs. It is important to note that although this process is done currently by the Andromeda team, the process of accepting and publishing ADOs will eventually be handed to the Andromeda DAO to make the process as decentralized as possible.&#x20;

{% hint style="warning" %}
ADOs instantiated by code Ids found in the ADODB are eligible to communicate with the [AMP ](./)layer as it ensures that the ADO was created by one of the Andromeda contracts.
{% endhint %}

**Ado\_type**: adodb

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

| Name             | Type            | Description                                                                                                            |
| ---------------- | --------------- | ---------------------------------------------------------------------------------------------------------------------- |
| `kernel_address` | String          | The contract address of the kernel\_address. Can be found in our [deployed contracts](<../deployed-contracts (1).md>). |
| `owner`          | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.            |

## ExecuteMsg

### Publish

Publishes an  ADO to the ADODB by saving the provided `code_id` under the specified `ado_type`.&#x20;

{% hint style="warning" %}
The new version needs to be greater than the old one if using an already published ado\_type.

Only available to the ADO owner.

The fee can be either a native or a CW20 asset.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
 pub enum ExecuteMsg {
   Publish {
        code_id: u64,
        ado_type: String,
        action_fees: Option<Vec<ActionFee>>,
        version: String,
        publisher: Option<String>,
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"publish":{
"code_id": 98,
"ado_type":"auction",
"action_fees": [
                {
                 "action":"Mint",
                 "asset":"native:uandr",
                 "amount":"500",
                 "receiver":"andr1...
                 },
                 {
                 "action":"Buy",
                 "asset":"native:uandr",
                 "amount":"700",
                 "receiver":"andr1...
                 }],
"version":"1.2.9",
"publisher":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                      | Description                                                                                                       |
| ------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `code_id`     | u64                                                       | The code id to be used to instantiate the specified `ado_type`.                                                   |
| `ado_type`    | String                                                    | The type/name of the ADO to publish.                                                                              |
| `action_fees` | Option\<Vec<[ActionFee](andromeda-factory.md#actionfee)>> | Optional set of fees to be enforced when perfroming some action on an ADO of this type.                           |
| `version`     | String                                                    | The version of the ADO. The version needs to conform with the[ semantic versioning](https://semver.org) standard. |
| `publisher`   | Option\<String>                                           | The address to receive the fees.                                                                                  |

#### ActionFee

{% hint style="warning" %}
Ensure asset is in the format "cw20:address" or "native:denom
{% endhint %}

```rust
pub struct ActionFee {
    pub action: String,
    pub asset: String,
    pub amount: Uint128,
    pub receiver: Option<Addr>,
}
```

| Name       | Type          | Description                                                                                                                                            |
| ---------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `action`   | String        | The action to add the fees on when called. This could be instantiation or one of the execute messages of the ADO.                                      |
| `asset`    | String        | The funds used to pay the fee. Can be either a native fund specified as "native:uandr" or a CW20 token contract address specified as "cw20:andr1....". |
| `amount`   | String        | The amount of the specified funds to pay as fees when performing the action.                                                                           |
| `receiver` | Option\<Addr> | An optional address to receive the fees. If not specified, then the specified publisher receives the fees.                                             |

### Unpublish

Unpublishes a previously published ADO.&#x20;

{% hint style="warning" %}
Only available to the contract owner.

Once unpublished, the code\_id will be removed from the ADODB.

Unplubish is for a specific version of an ADO.

An Unpublished ADO can never be published again.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
Unpublish {
        ado_type: String,
        version: String,
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"unpublish":{
"ado_type":"auction",
"version":"1.2.9"
    }
}
```
{% endtab %}
{% endtabs %}

### UpdateActionFees

Update the fees implemented on the specified ado\_type.&#x20;

{% hint style="warning" %}
Only availabe to the contract owner.

Will return an error if the ADO type does not exist.
{% endhint %}

{% tabs %}
{% tab title="Rust " %}
```rust
 pub enum ExecuteMsg {
 UpdateActionFees {
        ado_type: String,
        action_fees: Vec<ActionFee>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_action_fees":{
    "ado_type":"marketplace",
    "action_fees": [
                {
                 "action":"Mint",
                 "asset":"native:uandr",
                 "amount":"900",
                 "receiver":"andr1...
                 },
                 {
                 "action":" Buy",
                 "asset":"native:uandr",
                 "amount":"350",
                 "receiver":"andr1...
                 }]
   }
```
{% endtab %}
{% endtabs %}

| Name          | Type                                             | Description                                                         |
| ------------- | ------------------------------------------------ | ------------------------------------------------------------------- |
| `ado_type`    | String                                           | The `ado_type` to update the action fees for.                       |
| `action_fees` | Vec<[ActionFee](andromeda-factory.md#actionfee)> | The new fees to implement on the ADOs with the specified ADO type . |

### RemoveActionFees

Removes the specified Action Fees for the specified ado\_type.&#x20;

{% hint style="warning" %}
Only available to the contract owner.&#x20;

Will return an error if the ADO type does not exist.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg {
</strong>RemoveActionFees {
        ado_type: String,
        actions: Vec&#x3C;String>,
    },
  }
</code></pre>
{% endtab %}

{% tab title="JSON " %}
```json
{
"remove_action_fees":{
    "ado_type":"marketplace",
    "action_fees":["Mint","Buy","Sell",...]
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type         | Description                                   |
| ------------- | ------------ | --------------------------------------------- |
| `ado_type`    | String       | The `ado_type` to  remove the fees from.      |
| `action_fees` | Vec\<String> | A vector of Actions to remove the fees from.  |

### UpdatePublisher

Assigns a new address as the publisher of an ADO.

{% hint style="warning" %}
Only availabe to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust " %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum ExecuteMsg {  
</strong>  UpdatePublisher {
        ado_type: String,
        publisher: String,
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_publisher":{
    "ado_type":"auction",
    "publisher":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name        | Type   | Description                               |
| ----------- | ------ | ----------------------------------------- |
| `ado_type`  | String | The ado type to update the publisher for. |
| `publisher` | String | The address of the new publsiher.         |

### Ownership

The set of ownerhsip messages. These messages are the same as the ones found in the [ADO base section](../ado-base/andromedamsg-v1.0.0.md#ownership).

## QueryMsg

### CodeId

Query the `code_id` of the specified key.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(u64)]
    CodeId {
      key:String,
    }
}
```
{% endtab %}

{% tab title="JSON" %}
```javascript
{
    "code_id": {
    "key":"cw721"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                                     |
| ----- | ------ | ----------------------------------------------- |
| `key` | String | The type of ADO we want to get the code Id for. |

Returns a u64 which represents the `code_id`.

### IsUnpublishedCodeId

Checks the if the `code_id` specified has been unpublished.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
  #[returns(IsUnpublishedCodeIdResponse)]
  IsUnpublishedCodeId {
   code_id: u64 
   },
  }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"is_unpublished_code_id":{
    "code_id":98
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type | Description                                                  |
| --------- | ---- | ------------------------------------------------------------ |
| `code_id` | u64  | The code Id to check whether it has been unpublished or not. |

Returns true if the code Id has been unpublished and false otherwise.

### ADOType

Queries the ADO type and version linked to the specified `code_id`.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum QueryMsg {
</strong><strong>    #[serde(rename = "ado_type")]
</strong><strong>    #[returns(Option&#x3C;ADOVersion>)]
</strong>    ADOType {
          code_id: u64 
    }
  }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_type":{
     "code_id":13
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="257.66666666666663">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>code_id</code></td><td>u64</td><td>The code Id to return the ADO type for.</td></tr></tbody></table>

Returns the ADO type in a string. The type will contain the ADO name along with the version. For example: `"cw721@0.2.3"`

### AllADOTypes

Queries all the ADO types available with their latest versions.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum QueryMsg {
</strong><strong>    #[returns(Vec&#x3C;String>)]
</strong>    #[serde(rename = "all_ado_types")]
    AllADOTypes {
        start_after: Option&#x3C;String>,
        limit: Option&#x3C;u32>,
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_ado_types":{
     "start_after":"auction",
     "limit": 40
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                                                                      |
| ------------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `start_after` | Option\<String> | Optional ADO to start from. The ADOs are listed alphabetically. If specified, the query will only fetch ADOs after the specified start\_after.                   |
| `limit`       | Option\<u32>    | Optional limit to the number of the ADO types returned by the query. If not specified, the default limit will be 100. The maximum limit that can be set is 200.  |

The query will return each ADO type along with its latest version.&#x20;

### ADOVersions

Queries the available versions of the specified ADO type.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
#[returns(Vec<String>)]
#[serde(rename = "ado_versions")]
    ADOVersions {
        ado_type: String,
        start_after: Option<String>,
        limit: Option<u32>,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"ado_versions":{
    "ado_type":"auction",
    "start_after":"auction@0.2.1",
    "limit": 15
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type            | Description                                                                                                                                                     |
| ------------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ado_type`    | String          | The type of ADO to get the versions for.                                                                                                                        |
| `start_after` | Option\<String> | Optional version to start from. If specified, the query will only fetch versions greater than the start\_after version.                                         |
| `limit`       | Option\<u32>    | Optional limit to the number of the versions returned by the query. If not specified, the default limit will be 100. The maximum limit that can be set is 200.  |

Returns a Vec\<String> containing all the requested ADO versions.&#x20;

### ADOMetadata

Queries all the metadata related to the specified ADO type.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {  
  #[serde(rename = "ado_metadata")]
  #[returns(Option<ADOMetadata>)]
  ADOMetadata { ado_type: String },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_metadata":{
    "ado_type":"auction"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                            |
| ---------- | ------ | -------------------------------------- |
| `ado_type` | String | The ADO type to get the metadata for.  |

#### ADOMetadata

The struct returned containing the ADO metadata.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ADOMetadata {
    pub publisher: String,
    pub latest_version: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"publisher":"andr1...",
"latest_version":"1.4.8
}
```
{% endtab %}
{% endtabs %}

| Name             | Type   | Description                              |
| ---------------- | ------ | ---------------------------------------- |
| `publisher`      | String | The publisher of the specified ADO type. |
| `latest_version` | String | The published version of the ADO type.   |

### ActionFee

Queries the fees implemented on the specified Action for the specified ADO type.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Option<ActionFee>)]
    ActionFee { ado_type: String, action: String },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"action_fee":{
    "ado_type":"nft-auction",
    "action":"Mint"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                         |
| ---------- | ------ | --------------------------------------------------- |
| `ado_type` | String | The ADO type that has the action to query fees for. |
| `action`   | String | The action of the ADO type to query the fees for.   |

Returns the [ActionFee](andromeda-factory.md#actionfee) struct with the fee information if found.

### ActionFeeByCodeId

Queries the fees implemented on the specified Action for the specified code\_id ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Option<ActionFee>)]
    ActionFeeByCodeId { code_id: u64, action: String },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"action_fee_by_code_id":{
    "code_id": 55,
    "action":"Buy"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                         |
| ---------- | ------ | --------------------------------------------------- |
| `ado_type` | String | The ADO type that has the action to query fees for. |
| `action`   | String | The action of the ADO type to query the fees for.   |

Returns the [ActionFee](andromeda-factory.md#actionfee) struct with the fee information if found.

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
