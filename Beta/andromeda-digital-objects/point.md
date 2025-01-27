# Point

## Introduction

The Point ADO allows users to store the coordinates for one point. This ADO can then be referenced in the [Graph ADO](graph.md) to save this coordinate for the user.

The Point ADO can be set to one of the following:

* **Private:** Only accessible by the contract owner of the ADO.
* **Public:** Accessible by anyone.
* **Restricted:** Only accessible to the address that set the point the first time.

**Ado\_type**: point

**Version:** 0.1.0-beta

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub restriction:PointRestriction,
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

| Name             | Type                                          | Description                                                                                                                                                                                                                                                                                                             |
| ---------------- | --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `restriction`    | [PointRestriction](point.md#pointrestriction) | Specifies who has access specify the point coordiantes                                                                                                                                                                                                                                                                  |
| `kernel_address` | String                                        | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](../platform-and-framework/deployed-contracts.md). |
| `owner`          | Option\<String>                               | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                             |

#### PointRestriction

An enum defining the different types of restrictions that can be set.

```rust
pub enum PointRestriction {
    Private,
    Public,
    Restricted,
}
```

* **Private:** Only accessible by the contract owner of the ADO.
* **Public:** Accessible by anyone.
* **Restricted:** Only accessible to the key owner (The address that first set the point).

## ExecuteMsg

### SetPoint

Sets the point coordinates.

{% hint style="warning" %}
Authorization for this message depends on the set [PointRestriction](point.md#pointrestriction).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
SetPoint {
   point: PointCoordinate 
    },
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"set_point":{
    "point":{
        "x_coordinate": 25.66,
        "y_coordinate": 40.33,
        "z_coordinate": 17.9
          }
        },
```
{% endtab %}
{% endtabs %}

| Name    | Type                                        | Description                           |
| ------- | ------------------------------------------- | ------------------------------------- |
| `point` | [PointCoordinate](point.md#pointcoordinate) | The coordinates of the point to save. |

#### PointCoordinate

```rust
pub struct PointCoordinate {
    pub x_coordinate: SignedDecimal,
    pub y_coordinate: SignedDecimal,
    pub z_coordinate: Option<SignedDecimal>,
}

/// A signed fixed-point decimal value with 18 fractional digits.
/// The greatest possible value that can be represented is 
/// 170141183460469231731.687303715884105727 (which is (2^127 - 1) / 10^18)
/// and the smallest is
/// -170141183460469231731.687303715884105728 (which is -2^127 / 10^18).
pub struct SignedDecimal(#[schemars(with = "String")] Int128);
```

| Name           | Type            | Description                                                             |
| -------------- | --------------- | ----------------------------------------------------------------------- |
| `x_coordinate` | String          | The x coordinate for the point.                                         |
| `y_coordinate` | String          | The y coordinate for the point.                                         |
| `z_coordinate` | Option\<String> | The z coordinate for the point. Do not specify in case the graph is 2D. |

### DeletePoint

Deletes the data attached to set point.

{% hint style="warning" %}
Authorization for this message depends on the set [PointRestriction](point.md#pointrestriction).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg{
   DeletePoint {},
}
```
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json"><strong>{
</strong>"delete_point":{}
}
</code></pre>
{% endtab %}
{% endtabs %}

### UpdateRestriction

Changes the restriction set on the Point ADO.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 UpdateRestriction {
        restriction: PointRestriction,
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

| Name          | Type                                          | Description                                        |
| ------------- | --------------------------------------------- | -------------------------------------------------- |
| `restriction` | [PointRestriction](point.md#pointrestriction) | The new restriction type to use for the Point ADO. |

### Base Executes

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg

### GetPoint

Gets the coordinates of the stored point.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg { 
    #[returns(PointCoordinate)]
    GetPoint {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_point":{}
}
```
{% endtab %}
{% endtabs %}

#### GetValueResponse

{% tabs %}
{% tab title="Rust" %}
```rust
#[cw_serde]
pub struct PointCoordinate {
    pub x_coordinate: String,
    pub y_coordinate: String,
    pub z_coordinate: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
 "x_coordinate": 3,
 "y_coordinate": 6,
 "z_coordinate": 15
}
```
{% endtab %}
{% endtabs %}

| Name           | Type            | Description                     |
| -------------- | --------------- | ------------------------------- |
| `x_coordinate` | String          | The x coordinate for the point. |
| `y_coordinate` | String          | The y coordinate for the point. |
| `z_coordinate` | Option\<String> | The z coordinate for the point. |

### GetDataOwner&#x20;

Queries the address of the user who set the point coordinates.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
#[returns(GetDataOwnerResponse)]
    GetDataOwner {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_data_owner":{}
}
```
{% endtab %}
{% endtabs %}

#### GetDataOwnerResponse

```rust
#[cw_serde]
pub struct GetDataOwnerResponse {
    pub owner: AndrAddr,
}
```

| Name    | Type     | Description                                 |
| ------- | -------- | ------------------------------------------- |
| `owner` | AndrAddr | The address that set the point coordinates. |

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
