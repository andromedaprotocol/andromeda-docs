# Graph

## Introduction

The **Graph ADO** allows users to construct a customized graphs. The user first specifies the configurations of the graph at instantiation. Then they are able to store coordinates for points on the graph. These saved points can then be fetched by using the ADO's queries.

The graph can be a 3D graph (x,y, and z coordinates) or a 2D graph (x,y coordinates only).

This ADO can also save and fetch points stored in the[ Point ADO.](point.md)&#x20;

**Ado\_type**: cw721

**Version: 0.1.0-beta**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub map_info: MapInfo,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"map_info":{
    "map_size":{
        "x_width": 1000,
        "y_width": 1000,
        "z_width": 1000
        },
    "allow_negative": false,
    "map_decimal": 0
    },
"kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name             | Type                        | Description                                                                                                                                                                                                                                                                                                             |
| ---------------- | --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `map_info`       | [MapInfo](graph.md#mapinfo) | Specifies the configurations of the map.                                                                                                                                                                                                                                                                                |
| `kernel_address` | String                      | Contract address of the [kernel contract](../platform-and-framework/andromeda-messaging-protocol/kernel.md) to be used for [AMP](../platform-and-framework/andromeda-messaging-protocol/) messaging. Kernel contract address can be found in our [deployed contracts](../platform-and-framework/deployed-contracts.md). |
| `owner`          | Option\<String>             | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.                                                                                                                                                                                                             |

#### MapInfo

```rust
#[cw_serde]
pub struct MapInfo {
    pub map_size: MapSize,
    pub allow_negative: bool,
    pub map_decimal: u16,
}
```

| Name             | Type                        | Description                                                                                                                                                                                                                                                                                         |
| ---------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `map_size`       | [MapSize](graph.md#mapsize) | Specifies the size of the map.                                                                                                                                                                                                                                                                      |
| `allow_negative` | bool                        | Whether negative values can be assigned to points of the map. In case negative is allowed, the width of the axis is divided between the positive and negative halfs. For example if negative is allowed and the `x_width` is set as 1000, then the the x coordinate range is between -500 and 500.  |
| `map_decimal`    | u16                         | The max number of decimal points that can be specified for a point. For example, if the `x_coordinate` is set as 4.12345 and the `map_decimal` is set as 2, then the x coordinate is taken as 4.12 only.                                                                                            |

#### MapSize

```rust
#[cw_serde]
pub struct MapSize {
    pub x_width: u64,
    pub y_width: u64,
    pub z_width: Option<u64>,
}
```

| Name      | Type         | Description                                                                                          |
| --------- | ------------ | ---------------------------------------------------------------------------------------------------- |
| `x_width` | u64          | The maximum allowed x coordinate for the map.                                                        |
| `y_width` | u64          | The maximum allowed y coordinate for the map.                                                        |
| `z_width` | Option\<u64> | The maximum allowed z coordinate for the map. Can be ommited in case a 2D map is wanted by the user. |

## ExecuteMsg

### UpdateMap

Update the map configurations set at instantiation.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateMap {
        map_info: MapInfo,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_map":{
    "map_info":{
    "map_size":{
        "x_width": 3000,
        "y_width": 4000,
        "z_width": 5000
        },
    "allow_negative": true,
    "map_decimal": 2
    }
  }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type                        | Description                              |
| ---------- | --------------------------- | ---------------------------------------- |
| `map_info` | [MapInfo](graph.md#mapinfo) | Specifies the configurations of the map. |

### StoreCoordinate

Stores the coordinates of a point on the map.

{% hint style="warning" %}
Only available to the contract owner.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum ExecuteMsg {
<strong>    StoreCoordinate {
</strong>        coordinate: Coordinate,
        is_timestamp_allowed: bool,
        }
 }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"store_coordinate":{
    "coordinate":{
        "x_coordinate": 25.66,
        "y_coordinate": 40.33,
        "z_coordinate": -25.12
        },
    "is_timestamp_allowed": true
    }
}       
```
{% endtab %}
{% endtabs %}

| Name                   | Type                              | Description                                                                                                                    |
| ---------------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `coordinate`           | [Coordinate](graph.md#coordinate) | The coordinates of the point.                                                                                                  |
| `is_timestamp_allowed` | bool                              | Whether to store the timestamp that the point was created. If yes, then it would show along with the coordinates when queried. |

#### Coordinate

```rust
#[cw_serde]
pub struct Coordinate {
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

| Name           | Type                   | Description                                                                          |
| -------------- | ---------------------- | ------------------------------------------------------------------------------------ |
| `x_coordinate` | SignedDecimal          | The x coordinate for the point.                                                      |
| `y_coordinate` | SignedDecimal          | The y coordinate for the point.                                                      |
| `z_coordinate` | Option\<SignedDecimal> | The z coordinate for the point. Do not specify in case the map is 2D (Only x and y). |

### StoreUserCoordinate <a href="#base-executes" id="base-executes"></a>

Store the coordinates from the specified [Point ADO](point.md) addresses.

{% hint style="warning" %}
f the coordinates of the point ADO are changed, you need to call this message again on that Point ADO to store the new coordinates.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 StoreUserCoordinate {
        user_location_paths: Vec<AndrAddr>,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"store_user_coordinate":{
    "user_location_paths":["andr1...","andr1...",...]
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="258">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>user_location_paths</code></td><td>Vec&#x3C;AndrAddr></td><td>A vector containing the addresses of the point ADOs.</td></tr></tbody></table>

### DeleteUserCoordinate

Deletes the coordinate set for the specified point in the [Point ADO](point.md).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 DeleteUserCoordinate {
        user: AndrAddr,
    },
 }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"delete_user_coordinate":{
    "user": "andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="258">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>user</code></td><td>AndrAddr</td><td>The Point ADO contract address to remove. </td></tr></tbody></table>



### Base Executes <a href="#base-executes" id="base-executes"></a>

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg <a href="#querymsg" id="querymsg"></a>

### GetMapInfo

Queries the map configurations set at instantiation.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetMapInfoResponse)]
    GetMapInfo {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_map_info":{}
}
```
{% endtab %}
{% endtabs %}

#### GetMapInfoResponse

```rust
#[cw_serde]
pub struct GetMapInfoResponse {
    pub map_info: MapInfo,
}
```

Returns a [MapInfo](graph.md#mapinfo) struct.

### GetMaxPointNumber

Queries the total number of points created.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(GetMaxPointNumberResponse)]
    GetMaxPointNumber {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_max_point":{}
}
```
{% endtab %}
{% endtabs %}

#### GetMaxPointResponse

```rust
#[cw_serde]
pub struct GetMaxPointNumberResponse {
    pub max_point: u128,
}
```

| Name        | Type | Description                                      |
| ----------- | ---- | ------------------------------------------------ |
| `max_point` | u128 | The total number of points created on the graph. |

### GetAllPoints

Queries all the created points, fetching their coordinates and timestamps if enabled.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
 #[returns(GetAllPointsResponse)]
    GetAllPoints {
        start: Option<u128>,
        limit: Option<u32>,
   },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_all_points":{}
}
```
{% endtab %}
{% endtabs %}

| Name    | Type          | Description                                                             |
| ------- | ------------- | ----------------------------------------------------------------------- |
| `start` | Option\<u128> | Optional point to start from. Used for pagination.                      |
| `limit` | Option\<u32>  | Optional limit on the number of results to return. Used for pagination. |

#### GetAllPointsResponse

<pre class="language-rust"><code class="lang-rust">#[cw_serde]
<strong>pub struct GetAllPointsResponse {
</strong>    pub points: Vec&#x3C;(CoordinateInfo, StoredDate)>,
}

#[cw_serde]
pub struct CoordinateInfo {
    pub x: String,
    pub y: String,
    pub z: Option&#x3C;String>,
}

#[cw_serde]
pub struct StoredDate {
    pub timestamp: Option&#x3C;u64>,
}
</code></pre>

Returns a vector containing the coordinates of each point. Points that had timestamp enabled will have the timestamp returned as well.

### GetUserCoordinate

Fetches the coordinate saved for the specified Point ADO.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg { 
 #[returns(CoordinateInfo)]
    GetUserCoordinate { user: AndrAddr },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_user_coordinate":{
    "user":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type     | Description                                                       |
| ------ | -------- | ----------------------------------------------------------------- |
| `user` | AndrAddr | The address of the user who set the coordinates in the point ADO. |

Returns an instance of [CoordinateInfo](graph.md#getallpointsresponse).

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
