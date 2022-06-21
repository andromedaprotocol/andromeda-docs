---
description: >-
  The ADO_Base contains all the common functionality applied on all ADOs upon
  instantiation.
---

# ADO\_Base

### InstantiateMsg

The struct used to hold important information about each instantiated ADO. Implemented as `BaseInstantiateMsg`

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub ado_type: String,
    pub operators: Option<Vec<String>>,
    pub modules: Option<Vec<Module>>,
    pub primitive_contract: Option<String>,
}
```
{% endtab %}
{% endtabs %}

| Name                 | Type                                                        | Description                                                                                             |
| -------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `ado_type`           | String                                                      | The type of the ADO. Usually is the same as the name. Is automatically set when an ADO is instantiated. |
| `operators`          | Option\<Vec\<String>>                                       | The list of addresses to set as operators on the contract.                                              |
| `modules`            | Option\<Vec<[Module](../../modules/module-definitions.md)>> | The list of modules to add to the ADO.                                                                  |
| `primitive_contract` | Option\<String>                                             | The address of the primitive contract to use for data retrieval.                                        |
