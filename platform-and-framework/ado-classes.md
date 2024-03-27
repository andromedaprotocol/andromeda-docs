# ADO Classes

### Classes

The ADO classes bundle up ADOs with similar functionality. They provide a sort of filtering for our ADOs which is needed since more and more ADOs are being added frequently. This way, users can quickly find ADOs they are interested in based on the ADO class they belong to.

### Architecture Overview

#### Andromeda Digital Object

Each of our individual contracts is referred to as an ADO. Each ADO has a specific purpose and custom messages specific to its use.

#### ADO-Base

Upon instantiating any ADO, a `ADOContract` struct is instantiated that holds key information on the ADO:

```rust
pub struct ADOContract<'a> {
    pub(crate) owner: Item<'a, Addr>,
    pub(crate) original_publisher: Item<'a, Addr>,
    pub(crate) block_height: Item<'a, u64>,
    pub(crate) ado_type: Item<'a, String>,
    pub(crate) app_contract: Item<'a, Addr>,
    pub(crate) kernel_address: Item<'a, Addr>,
    pub(crate) permissioned_actions: Map<'a, String, bool>,
}
```

To avoid defining these fields for every single contract, we have decided to bundle them in a struct that can be used by all ADOs. There are also [ado-base messages](ado-base/) which are the messages that can be called by any ADO (Unless they require a certain feature enabled) that will be further discussed in [ADO Base](ado-base/).
