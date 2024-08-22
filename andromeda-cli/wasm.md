# Wasm

### Available Commands

| Command                                | Description                         |
| -------------------------------------- | ----------------------------------- |
| [**execute**](wasm.md#execute)         | Executes a wasm message.            |
| [**instantiate**](wasm.md#instantiate) | Instantiates a contract by code Id. |
| [**migrate**](wasm.md#migrate)         | Migrate a contract.                 |
| [**query**](wasm.md#query)             | Queries a contract.                 |
| [**upload**](wasm.md#upload)           | Upload a contract wasm.             |

## Execute

Executes a wasm message.

#### Usage

```
wasm execute <contract address> <message>
```

#### Flags

<table><thead><tr><th>Flag</th><th width="235.66666666666666">Description</th><th>Usage</th></tr></thead><tbody><tr><td><strong>funds</strong></td><td>Funds to send with the message</td><td>--funds 100uandr</td></tr><tr><td><strong>memo</strong></td><td>An optional memo to attach to the message</td><td>--memo 'Wow what a great transaction!'</td></tr><tr><td><strong>simulate</strong></td><td>Simulates the transaction without broadcasting it. Useful to estimate gas costs.</td><td>-- simulate</td></tr><tr><td><strong>help</strong></td><td>Displays info about the current command. </td><td>--help</td></tr></tbody></table>

#### Example

```
? main-star@elgafar-1> wasm execute stars12w4nvet0fr2kh8a6xg594d4fd3tkwulrpy55nawh8s0y2z584pjsq4w2cq '{"deposit": {}}' --funds 10000ustars
```

The CLI will promt a confirmation. After confirming, the output is similar to:

```
– Executing Tx...

Transaction executed!

https://testnet-explorer.publicawesome.dev/stargaze/tx/042353250E0D6C67C320195032CB0BB7EDE1873F2434426A9812551ED4206E3E

The link will take you to the explorer to see the transaction details.
```

## Instantiate

Instantiates a contract by code Id.

#### Usage

```
wasm instantiate <code-id> <instantiate message>
```

#### Flags

| Flag         | Description                                                                      | Usage                           |
| ------------ | -------------------------------------------------------------------------------- | ------------------------------- |
| **label**    | Used to provide a label assigned to the instantiation.                           | --label 'This is a great label' |
| **admin**    | Used to provide an alternative admin address for the contract.                   | --admin andr1...                |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | --simulate                      |
| **print**    | Prints the constructed message before simulating.                                | --print                         |
| **help**     | Displays info about the current command.                                         | --help                          |

#### Example

```
? main-star@elgafar-1> wasm instantiate 3886 '{"kernel_address":"stars130mz3y4ajamadf9u5df7xh64t5ed5h3cxechz3jw43qdrc7wm09sxtykmx"}'
– Simulating Instantiation Tx...
Transaction simulated!

Cost Estimates
Gas Used: 211327
Fee estimates:
   5284ustars

? Do you want to proceed? Yes
- Instantiating your contract...

Contract instantiated!

https://testnet-explorer.publicawesome.dev/stargaze/tx/75184641D8275A5B34183CF31181C563FB6E4D007FC7BBC805D2DA43892BBBD7
Address: stars1zkug84swrqhvxupsuk4zu93zdcgva02uwlfk2095py39n7243vnq3dxd7y
```

{% hint style="warning" %}
The transaction link, as well as the new contract address are returned.
{% endhint %}

## Migrate

Migrates a contract.

#### Usage:&#x20;

```
wasm migrate <contract address> <new code id> <migrate msg>
```

#### Example:

```
wasm migrate 126 stars1zkug84swrqhvxupsuk4zu93zdcgva02uwlfk2095py39n7243vnq3dxd7y {}
```

## Query

Performs a contract query.

#### Usage

```
wasm query <contract address> <query object> 
```

#### Example

```
? main-star@elgafar-1> wasm query stars1zkug84swrqhvxupsuk4zu93zdcgva02uwlfk2095py39n7243vnq3dxd7y {"get_locked_funds_for_recipient":{"recipient":"stars1dtlndrtpth240retkn8l54w8gg2g4hzpq3ugas"}}
```

{% hint style="warning" %}
Unlike the other messages, the query does not require single quotes around the message.
{% endhint %}

Here I am queriying the account of a user (which is empty) from one of our contracts, we get:

```
[] 
```

## Upload

Upload a contract wasm.

{% hint style="warning" %}
The path is relative to where the CLI was run.&#x20;
{% endhint %}

#### Usage

```
wasm upload <wasm file> 
```

#### Example

```
wam upload andromeda_app_contract.wasm
```
