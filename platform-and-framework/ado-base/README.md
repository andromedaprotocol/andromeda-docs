---
description: >-
  The ADO Base contains all the common functionality applied on all ADOs upon
  instantiation.
---

# ADO Base

### InstantiateMsg

The struct used to hold important information about each instantiated ADO. Implemented as `BaseInstantiateMsg`.

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub ado_type: String,
    pub ado_version: String,
    pub kernel_address: String,
    pub owner: Option<String>,
}
```
{% endtab %}
{% endtabs %}

<table><thead><tr><th width="250.66666666666669">Name</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td><code>ado_type</code></td><td>String</td><td>The type of the ADO. Usually, it is the same as the name. It is automatically set when an ADO is instantiated.</td></tr><tr><td><code>ado_version</code></td><td>String</td><td>The version of the ADO. It is automatically set when an ADO is instantiated.</td></tr><tr><td><code>kernel_address</code></td><td>Option&#x3C;String></td><td>The contract address of the <a href="../andromeda-messaging-protocol/kernel.md">kerne</a><a href="../andromeda-messaging-protocol/kernel.md">l</a> ADO responsible for communication between ADOs. Specified by the creator in the instantiation message of an ADO.</td></tr><tr><td><code>owner</code></td><td>Option&#x3C;String></td><td>The address of the ADO owner. Specified by the creator in the instantiation message of an ADO. </td></tr></tbody></table>

Our ADOs have a set of execute and query messages referred to as the "base executes" and "base queries". These messages are common to every ADO in our Andromeda Digital Library (Except AMP ADOs). They are listed in the **AndromedaMsg** and **AndromedaQuery** enums which we will discuss next. &#x20;
