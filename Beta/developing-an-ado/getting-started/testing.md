# Testing

## Testing

To aid in testing we provide a separate `mock_dependencies_custom` function that provides handlers for specific ADO contracts. This can be found [here](https://github.com/andromedaprotocol/andromeda-core/blob/1.0.rc-1/packages/std/src/testing/mock\_querier.rs). The following mock variables are used:

```rust
/// Mock CW20 Contract Address
pub const MOCK_CW20_CONTRACT: &str = "cw20_contract";
/// Mock Anchor Contract Address
pub const MOCK_ANCHOR_CONTRACT: &str = "anchor_contract";
/// Mock App Contract Address
pub const MOCK_APP_CONTRACT: &str = "app_contract";
/// Mock Primitive Contract Address
pub const MOCK_PRIMITIVE_CONTRACT: &str = "primitive_contract";
/// Mock Kernel Contract Address
pub const MOCK_KERNEL_CONTRACT: &str = "kernel_contract";
/// Mock Kernel Contract Address on foreign chain
pub const MOCK_FAKE_KERNEL_CONTRACT: &str = "fake_kernel_contract";
/// Mock VFS Contract Address
pub const MOCK_VFS_CONTRACT: &str = "vfs_contract";
/// Mock ADODB Contract Address
pub const MOCK_ADODB_CONTRACT: &str = "adodb_contract";
// Mock ADO Publisher
pub const MOCK_ADO_PUBLISHER: &str = "ado_publisher";
// Mock Osmosis Router
pub const MOCK_OSMOSIS_ROUTER_CONTRACT: &str = "osmosis_router";
// Mock Economics Contract
pub const MOCK_ECONOMICS_CONTRACT: &str = "economics_contract";

/// Mock Rates Contract Address
pub const MOCK_RATES_CONTRACT: &str = "rates_contract";
/// Mock Address List Contract Address
pub const MOCK_ADDRESS_LIST_CONTRACT: &str = "address_list_contract";

/// An invalid contract address
pub const INVALID_CONTRACT: &str = "invalid_contract";
/// An invalid VFS Path
pub const FAKE_VFS_PATH: &str = "/f";
/// An invalid ADODB Key
pub const FAKE_ADODB_KEY: &str = "fake_adodb_key";
/// A valid action
pub const MOCK_ACTION: &str = "action";
```

To use the dependencies simply create your mock dependencies variable like so:

```rust
use andromeda_std::testing::mock_dependencies_custom;

fn test_something() {
 let mut deps = mock_dependencies_custom(&[]);
 // .. The rest of your test code
}
```

## Integration Testing

A `cw-multi-test` based testing library for integration testing custom ADO contracts and how they interact with the aOS. Source code is found [here](https://github.com/andromedaprotocol/andromeda-core/tree/1.0.rc-1/packages/andromeda-testing).

### Getting Started

To get started you must first create an instance of the `MockAndromeda` struct:

```rust
use cosmwasm_std::Addr;
use cw_multi_test::App;
use andromeda_testing::MockAndromeda;

fn mock_andromeda(app: &mut App, admin_address: Addr) -> MockAndromeda {
    MockAndromeda::new(app, &admin_address)
}
```

This process will set up the aOS mock contracts and make them accessible through this struct.

```rust
pub struct MockAndromeda {
    pub admin_address: Addr,
    pub kernel: MockKernel,
    pub adodb: MockADODB,
    pub economics: MockEconomics,
    pub vfs: MockVFS,
}
```

The `admin_address` has ownership of the aOS and as such can make adjustments as required. It is also registered under the VFS as user `am`, therefore any ADOs registered with the admin address would be accessible via `~/am/...` when using the VFS.

The next step is to add some ADOs to the setup, this can be done with the `store_ado` method:

```rust
let andr = mock_andromeda();
andr.store_ado(&mut router, mock_andromeda_app(), "app");
```

Here the second parameter is a `cw-multi-test` [mock contract](https://docs.rs/cw-multi-test/latest/cw\_multi\_test/trait.Contract.html) and the third is a name for the ADO. This can be used to access the code id by calling `andr.get_code_id(&router, "app")`. Repeat this process for any ADOs you wish to add (including your own).

### Creating a Mock Contract

All our mock contracts implement two structs; `MockContract` and `MockADO`. The `MockContract` struct exposes simple query and execute methods while `MockADO` exposes any messages that an ADO may provide such as ownership and permissioning. To implement both these structs in a quick and easy manner we have provided a macro:

```rust
use andromeda_test::{mock_ado, MockADO, MockContract};
use crate::msg::{ExecuteMsg, QueryMsg};

pub struct MyMockADO(Addr);
mock_ado!(MyMockADO, ExecuteMsg, QueryMsg);

impl MyMockADO {
 // Expose any useful methods in here
}
```

Once this is done you can either create the contract directly or you can create it as part of an app. If you create it using the `MockApp`struct provided in `andromeda-app-contract` the mock contract struct can be created via a query:

```rust
  let app = MockApp::instantiate(...);
  let my_ado: MyMockADO = app.query_ado_by_component_name(&router, my_ado_name);
```

Mock structs have been provided for most ADOs however they are still a work in progress.

### Test Scaffolding

To help with setting up a testing environment using aOS we can use the `MockAndromedaBuilder` struct. This allows definition of what wallets and contracts you would like to use while testing:

```rust
use andromeda_testing::{mock::mock_app, mock_builder::MockAndromedaBuilder};

let mut router = mock_app(None);
let andr = MockAndromedaBuilder::new(&mut router, "admin")
    .with_wallets(vec![
        ("owner", vec![coin(1000, "uandr")]),
        ("user1", vec![]),
    ])
    .with_contracts(vec![
        ("cw721", mock_andromeda_cw721()),
        ("app-contract", mock_andromeda_app()),
    ])
    .build(&mut router);
```

In the above example we specify a few things:

```rust
MockAndromedaBuilder::new(&mut router, "admin")
```

Here we set the wallet **name** that has admin privileges over the aOS, in this case `"admin"`. Next up we define a few extra wallets:

```rust
.with_wallets(vec![
  ("owner", vec![coin(1000, "uandr")]),
  ("user1", vec![]),
])
```

This generates two wallets (`"owner"` and `"user1"`) and assigns 1000 uandr to the `"owner"` wallet. Here the provided names **are not addresses** but are simply names. These can be accessed using:

```rust
let owner = andr.get_wallet("owner");
let user1 = andr.get_wallet("user1");
```

Next we include the contracts we would like to use:

```rust
.with_contracts(vec![
    ("cw721", mock_andromeda_cw721()),
    ("app-contract", mock_andromeda_app()),
])
```

In this case we would like to use the CW721 contract and the App contract, the provided names can also be version (e.g. `"cw721@0.1.0"`). The stored code IDs for these can be accessed via their names like so:

```rust
let app_code_id = andr.get_code_id("cw721");
```

The rest of the integration test should continue as usual.
