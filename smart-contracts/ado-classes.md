# ADO Classes

#### Classes

The ADO classes bundle up ADOs with similar functionality. They provide a sort of filtering for our ADOs which is needed since more and more ADOs are being added frequently. This way, users can quickly find ADOs they are interested in based on the ADO class they belong to.

### Architecture Overview

#### ADOs

Each of our individual contracts is referred to as an ADO. Each ADO has a specific purpose and custom messages.&#x20;

#### Modules

Modules are ADOs that can be attached to other ADOs to extend functionality. Modules can be standalone contracts but most of the time are used as additions to other more complex ADOs. An example of a module would be our [rates ](../modules/rates-module.md)module that can apply rates automatically on any ADO that implements buying/ selling, or the [address list](../modules/address-list-module/) module that can whitelist addresses to interact with another ADO. Only ADOs that logically benefit from the addition of modules have the modules feature enabled. If an ADO can attach modules then it would be an optional field in the instantiation message. Modules are implemented using [hooks ](broken-reference)and queries that will be further explained in other sections.&#x20;

#### Primitive

A primitive is an ADO whos sole purpose is to store values. Primitives are attached to ADOs that utilize it. Most of the ADOs that require a primitive tend to have some contract addresses that need to be stored. The primitive ADO is also used to store the Factory address for building Andromeda Apps.

#### ADO-Base

Upon instantiating any ADO, a `ADOContract` struct is instantiated that holds key information on the ADO:

```rust
pub struct ADOContract<'a> {
    pub(crate) owner: Item<'a, Addr>,
    pub(crate) operators: Map<'a, &'a str, bool>,
    pub(crate) ado_type: Item<'a, String>,
    pub(crate) app_contract: Item<'a, Addr>,
    #[cfg(feature = "primitive")]
    pub(crate) primitive_contract: Item<'a, Addr>,
    #[cfg(feature = "primitive")]
    pub(crate) cached_addresses: Map<'a, &'a str, String>,
    #[cfg(feature = "modules")]
    pub(crate) module_info: Map<'a, &'a str, Module>,
    #[cfg(feature = "modules")]
    pub(crate) module_idx: Item<'a, u64>,
    #[cfg(feature = "withdraw")]
    pub withdrawable_tokens: Map<'a, &'a str, AssetInfo>,
}
```

To avoid defining these fields for every single contract, we have decided to bundle them in a struct that can be used by all ADOs. There are also [ado-base messages](../ado\_base/andrreceive-andrquery.md) which are the messages that can be called by any ADO (Unless they require a certain feature enabled) that will be further discussed later on.

As we can see, not all ADOs use all the fields. Some require that the ADO have the modules, primitive, or withdraw feature which will be stated in that ADO documentation.&#x20;

Each ADO has a type which will be specified in the contract's page.

### What's Next

In the following sections we will be going through each of our unique ADOs in each of our classes.&#x20;

We will provide the following for each ADO:

* An introduction to the ADO and its function.
* Contract messages ( Instantiation, Executes, Queries).
* &#x20;Rust and JSON representation along with explanations and field definitions for each of the messages.
