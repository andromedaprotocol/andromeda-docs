---
description: Send CosmWasm messages to the chain.
---

# Wasm

### Available Commands

| Command         | Description                         |
| --------------- | ----------------------------------- |
| **execute**     | Executes a wasm message.            |
| **instantiate** | Instantiates a contract by code ID. |
| **migrate**     | Migrate a contract.                 |
| **query**       | Queries a contract.                 |
| **upload**      | Upload a contract wasm.             |

## Execute

Executes a wasm message.

#### Usage

```
wasm execute <contract address> <message>
```

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simulate                            |
| **help**     | Displays info about the current command.                                         | --help                                 |

#### Example

```
? $test@uni-3> wasm execute juno1jtwyahp6mzntaash8gylzfzusfcqapgzf78r2vfxllxcl4z4c8pqx77sg3 '{"deposit": {}}' --funds 10000ujunox
```

The CLI will promt a confirmation. After confirming, the output is similar to:

```
– Executing Tx...

Transaction executed!

https://testnet.mintscan.io/juno-testnet/txs/738481C4404F7EF0FF5AEA438448BA33C0E8E3D3D036481F822523D99ED40DB5
```

The link will take you to the explorer to see the transaction details.

## Instantiate

Instantiates a contract by code ID.

#### Usage

```
wasm instantiate <code-id> <instantiate message>
```

#### Flags

| Flag   | Description                                                    | Usage                           |
| ------ | -------------------------------------------------------------- | ------------------------------- |
| label  | Used to provide a label assigned to the instantiation.         | --label 'This is a great label' |
| admin  | Used to provide an alternative admin address for the contract. | --admin juno...                 |
| help   | Displays info about the current command.                       | --help                          |

#### Example

```
? $test@uni-3> wasm instantiate 572 '{}'
– Instantiating your contract...

Contract instantiated!

https://testnet.mintscan.io/juno-testnet/txs/4546FED1F4D2A92EBEDE58FB4440FF1324473ED11DFEC6B29AC37E566CD3CCB7
Address: juno1hlp92l8mhkpf9gusaklnqlyu07y2vygvru6m85fvmcqnrt7y0vcq6up78z
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
wasm migrate juno1hlp92l8mhkpf9gusaklnqlyu07y2vygvru6m85fvmcqnrt7y0vcq6up78z {}
```

## Query

Performs a contract query.

#### Usage

```
wasm query <contract address> <query object> 
```

#### Example

```
 ? $test@uni-3> wasm query juno1kfl9qxw55hm50ns24hg482hfvh2hdspjpdwq008rue3f84vhu07spwgtky {"balance":{"address":"juno197y44w58djquqkpu8v56p
gwvm058f6jswm0jec"}} 
```

{% hint style="warning" %}
Unlike the other messages, the query does not require single quotes around the message.
{% endhint %}

Here I am queriying the account of a user from one of our contracts, we get:

```
[ { denom: 'ujunox', amount: '335' } ] 
```

## Upload

Upload a contract wasm.

#### Usage

```
wasm upload <wasm file> 
```

#### Example

```
wam upload andromeda_app_contract.wasm
```
