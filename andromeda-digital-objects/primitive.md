# Primitive

## Introduction

The **Primitive ADO** is a smart contract that is used to store data. It is a simple contract that allows us to store key/value pairs acting like a database.

The Primitive ADO can be set to one of the following:

* **Private:** Only accessible by the contract owner of the ADO.
* **Public:** Accessible by anyone.
* **Restricted:** Only accessible to the key owner.

{% hint style="info" %}
You can add a fee for setting keys using our rates.
{% endhint %}

**Ado\_type**: primitive

**Version: 2.0.2-beta.1**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub restriction:PrimitiveRestriction,
    pub kernel_address: String,
    pub owner: Option<String>
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"restriction":"public",
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                                                      | Description                                                                                                                                                                                                                                                                                                                   |
| ---------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `restriction`    | [PrimitiveRestriction](primitive.md#primitiverestriction) | Specifies who has access to add/delete data into the primitive ADO.                                                                                                                                                                                                                                                           |
| `kernel_address` | String                                                    | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](<../platform-and-framework/deployed-contracts (1).md>). |
| `owner`          | Option\<String>                                           | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                                   |

#### PrimitiveRestriction

An enum defining the different types of restrictions that can be set.

```rust
pub enum PrimitiveRestriction {
    Private,
    Public,
    Restricted,
}
```

* **Private:** Only accessible by the contract owner of the ADO.
* **Public:** Accessible by anyone.
* **Restricted:** Only accessible to the key owner (The address that first set the key).

## ExecuteMsg

### SetValue

Sets a value for the named key. When we need to extract the value that has been saved we would use the key.

{% hint style="warning" %}
If `key`is not specified the default key ("default") will be used.

If `SetValue` uses a `name` that is already in use, the old `value` is overwritten by the latest `value.`
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  SetValue {
        key: Option<String>,
        value: Primitive,
     }
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "set_value": {
    "key": "rate_name",
    "value": {
      "coin": {
        "denom": "uandr",
        "amount": "100"
      }
    }
  }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                | Description                                                                          |
| ------- | ----------------------------------- | ------------------------------------------------------------------------------------ |
| `key`   | Option\<String>                     | The key for the data. The default key "default" will be used if it is not specified. |
| `value` | [Primitive](primitive.md#primitive) | The value of the data.                                                               |

#### Primitive

An enum to specify the type of data you are saving.

```rust
pub enum Primitive {
    Uint128(Uint128),
    Decimal(Decimal),
    Coin(Coin),
    Addr(Addr),
    String(String),
    Bool(bool),
    Binary(Binary),
}
```

### DeleteValue

Deletes the data attached to the specified `key`.

{% hint style="warning" %}
If `key` is not specified the default key ("default") will be used.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
   pub enum ExecuteMsg{
   DeleteValue {
        key: Option<String>,
    }
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"delete_value":{
 "key":"rate_name"
    }
 }

```
{% endtab %}
{% endtabs %}

| Name  | Type            | Description                                                                                |
| ----- | --------------- | ------------------------------------------------------------------------------------------ |
| `key` | Option\<String> | Thel key for the data to delete. If not specified, the default key "default" will be used. |

### UpdateRestriction

Changes the restriction set on the primitive.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 UpdateRestriction {
        restriction: PrimitiveRestriction,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_restriction":{
    "restriction":"private"
    }
}
```
{% endtab %}
{% endtabs %}

| Name          | Type                                                      | Description                                           |
| ------------- | --------------------------------------------------------- | ----------------------------------------------------- |
| `restriction` | [PrimitiveRestriction](primitive.md#primitiverestriction) | The new restriction type to use for the primitve ADO. |

### Rates

Sets a fee on adding values to the primitive.

{% hint style="warning" %}
Only available to the contract owner.

Only a Flat rate type is accepted.
{% endhint %}

You can read more [here](../platform-and-framework/ado-base/andromedamsg.md#rates) about setting rates.

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### GetValue

Gets the value asociated to the specified key.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg { 
 #[returns(GetValueResponse)]
    GetValue { key: Option<String> },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_value":{
    "key":"funds"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type            | Description                                                                   |
| ----- | --------------- | ----------------------------------------------------------------------------- |
| `key` | Option\<String> | The key to get the value for. The default key is used if it is not specified. |

#### GetValueResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct GetValueResponse {
    pub key: String,
    pub value: Primitive,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"key":"funds",
"value": {
      "coin": {
        "denom": "uandr",
        "amount": "100"
      }
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                | Description                            |
| ------- | ----------------------------------- | -------------------------------------- |
| `key`   | String                              | The key you are getting the value for. |
| `value` | [Primitive](primitive.md#primitive) | The value for the specified key.       |

### AllKeys&#x20;

Gets all the keys that are currently saved in the primitive ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 #[returns(Vec<String>)]
    AllKeys {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"all_keys":{}
}
```
{% endtab %}
{% endtabs %}

Returns a Vec\<String> containing all the available keys.

### OwnerKeys

Gets all the keys that belong to the specified owner address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(Vec<String>)]
    OwnerKeys { owner: AndrAddr },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner_keys":{
    "owner":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type                                                           | Description                      |
| ------- | -------------------------------------------------------------- | -------------------------------- |
| `owner` | [AndrAddr](../platform-and-framework/common-types.md#andraddr) | The address to get the keys for. |

Returns a Vec\<String> containing all the keys belonging to the specified `owner`.

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
