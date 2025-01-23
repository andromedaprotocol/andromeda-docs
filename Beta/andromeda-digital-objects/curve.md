# Curve

## Introduction

The **Curve ADO** allows users to to move a value along a curve. The owner of the ADO can specify the exact curve equation they desire. The curve can be either a growth (Value increasing) or decay (Value decreasing) based on what the owner specifies.

Exponential curves have the following form:

$$
y= ab^{cx}
$$

Where:

&#x20;`a` is the base value,

`b` is the constant value,

and `c` is the multiple variable value.

{% hint style="warning" %}
For decay curves, the formula would be:&#x20;

$$
y= ab^{-cx}
$$
{% endhint %}

The constants `a`, `c`, and `b` are defined at instantiation. Users can then query the equation for the `y` value given the `x` value.

{% hint style="warning" %}
Currently only exponential curves are allowed.
{% endhint %}

**Ado\_type**: curve

**Version: 0.1.1-beta**

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub curve_config: CurveConfig,
    pub authorized_operator_addresses: Option<Vec<AndrAddr>>,
    pub kernel_address: String,
    pub owner: Option<String>,
} 
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "curve_config": {
      "curve_id": "growth",
      "base_value": 5,    
      "multiple_variable_value": 3, 
      "constant_value": 10  
  },
 "kernel_address":"andr1..."
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="330">Name</th><th width="214">Type</th><th>Description</th></tr></thead><tbody><tr><td><code>curve_config</code></td><td><a href="curve.md#curveconfig">CurveConfig</a></td><td>Specifies the curve equation to use.</td></tr><tr><td><code>authorized_operator_addresses</code></td><td>Option&#x3C;Vec&#x3C;AndrAddr>></td><td>Optional set of addresses that are authorized to update  or reset the curve. </td></tr><tr><td><code>kernel_address</code></td><td>String</td><td>Contract address of the <a href="../platform-and-framework/andromeda-messaging-protocol/kernel.md">kernel contract</a> to be used for <a href="../platform-and-framework/andromeda-messaging-protocol/">AMP</a> messaging. Kernel contract address can be found in our <a href="../platform-and-framework/deployed-contracts.md">deployed contracts</a>.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified.</td></tr></tbody></table>

#### CurveConfig

Specifies the equation of the curve. The equation is of the form:                                                                   &#x20;

$$
y= ab^{cx}
$$

{% hint style="warning" %}
For decay curves, the formula would be:



$$
y= ab^{-cx}
$$
{% endhint %}

```rust
#[cw_serde]
pub enum CurveConfig {
    ExpConfig {
        curve_id: CurveId,
        base_value: u64,
        multiple_variable_value: Option<u64>,
        constant_value: Option<u64>,
    },
}

#[cw_serde]
pub enum CurveId {
    Growth,
    Decay,
}
```

<table><thead><tr><th width="281">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>curve_id</code></td><td>CurveId</td><td>Whether to use a growing curve (multiple variable is taken as positive) or a decaying curve (multiple variable is taken as negative).</td></tr><tr><td><code>base_value</code></td><td>u64</td><td>Specifies the <code>a</code> constant.</td></tr><tr><td><code>multiple_variable_value</code></td><td>Option&#x3C;u64></td><td>Specifies the <code>c</code> constant. Defaults to 1 if not specified.</td></tr><tr><td><code>constant_value</code></td><td>Option&#x3C;u64></td><td>Specifies the <code>b</code> constant. Defaults to 0 if not specified.</td></tr></tbody></table>

#### Examples

| Curve Id  | Base Value | Multiple Value | Constant Value | Equation          |
| --------- | ---------- | -------------- | -------------- | ----------------- |
| `Growth`  | `4`        | `3`            | `10`           | $$y = 10*4^{3x}$$ |
| `Decay`   | `7`        | `3`            | `8`            | $$y = 8*7^{-3x}$$ |
| `Growth`  | `2`        | undefined      | undefined      | $$y = 2^x$$       |



## ExecuteMsg

### UpdateCurveConfig

Updates the curve equation set at instantiation.

{% hint style="warning" %}
Only available to the contract owner or one of the authorized addresses.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpdateCurveConfig {
         curve_config: CurveConfig 
         }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "update_curve_config": {
    "curve_config": {
      "curve_id": "growth",
      "base_value": 5,
      "multiple_variable_value": 2,
      "constant_value": 1
    }
  }
}
```
{% endtab %}
{% endtabs %}

### Reset

Removes the curve data from the ADO. If called, the curve will no longer exist. [**UpdateCurveConfig**](curve.md#updatecurveconfig) can be called to create a new one.

{% hint style="warning" %}
Only available to the contract owner  or one of the authorized addresses.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
  Reset {},
  }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"reset":{}
}
```
{% endtab %}
{% endtabs %}

### Base Executes <a href="#base-executes" id="base-executes"></a>

The rest of the execute messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.

## QueryMsg <a href="#querymsg" id="querymsg"></a>

### GetCurveConfig

Queries the curve equation values set by the owner.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
#[returns(GetCurveConfigResponse)]
    GetCurveConfig {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_curve_config":{}
}
```
{% endtab %}
{% endtabs %}

#### GetCurveConfigResponse

```rust
#[cw_serde]
pub struct GetCurveConfigResponse {
    pub curve_config: CurveConfig,
}
```

Returns a [CurveConfig](curve.md#curveconfig) struct.

### GetPlotYFromX

Specify the X value for the equation and get the Y value.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
#[returns(GetPlotYFromXResponse)]
    GetPlotYFromX { x_value: f64 },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"get_plot_y_from_x":{
    "x_value": "3"
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                                    |
| --------- | ------ | ---------------------------------------------- |
| x`_value` | String | The x value to be used to compute the y value. |

#### GetPlotYFromResponse

```rust
#[cw_serde]
pub struct GetPlotYFromXResponse {
    pub y_value: String,
}
```

| Name      | Type   | Description                                              |
| --------- | ------ | -------------------------------------------------------- |
| `y_value` | String | The y value that is computed from the specified x value. |

### Base Queries

The rest of the query messages can be found in the[ ADO Base](../platform-and-framework/ado-base/) section.
