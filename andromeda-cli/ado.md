---
description: Query and execute on an ADO.
---

# ADO

### Available commands

| Command       | Description                                        |
| ------------- | -------------------------------------------------- |
| **create**    | Creates an ADO by given type.                      |
| **execute**   | Executes a message on an ADO by given address.     |
| **factory**   | Allows executing and querying for a Factory ADO.   |
| **primitive** | Allows executing and querying for a Primitive ADO. |
| **query**     | Queries an ADO by given address.                   |
| **type**      | Queries the type of ADO for a given address.       |

## Create

Creates and ADO based on the specified ADO type.

#### Usage:

```
ado create <ado-type> 
```

#### Example:

```
ado create timelock
```

We are then prompted to add the information needed to create the timelock ADO.&#x20;

#### Flags:

| Flag         | Description                                                                      | Usage                                   |
| ------------ | -------------------------------------------------------------------------------- | --------------------------------------- |
| **label**    | Used to provide a label assigned to the instantiation                            | --label 'Wow what a great transaction!' |
| **admin**    | Used to provide an alternative admin address for the contract                    | -- admin juno1...                       |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simulate                             |
| **help**     | Displays info about the current command.                                         | --help                                  |

## Execute

Executes a message on an ADO by given contract address.&#x20;

#### Usage:

```
ado execute <contract address> 
```

#### Example:

```
ado execute juno153xscnqm7erc847fye9p2vuqtz6465g2jr48ah5meuwzjmzlc6aqf677yd
```

You can then chose from the list of executes to run.

#### Flags:

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **help**     | Displays info about the current command.                                         | --help                                 |

## Factory

Allows executing and querying for a Factory ADO.

| Command          | Description                                          |
| ---------------- | ---------------------------------------------------- |
| **address**      | Gets the address of the factory contract being used. |
| **updatecodeid** | Updates the code ID for a given ADO in the factory.  |

### Factory Address

Gets the address of the factory contract being used.

#### Usage

```
ado factory address
```

#### Example

```
// Command
? $test@uni-3> ado factory address

// Response
juno1alwx0jf72y366627gavklj6rte6ygdm4kw0ctqjrr4qg4y74h84sdfct3g
```

### Factory Updatecodeid

Updates the code ID for a given ADO in the factory.

### Usage

```
ado factory updatecodeid <ado key> <code id>
```

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **help**     | Displays info about the current command.                                         | --help                                 |

#### Example

```
ado factory updatecodeid auction 367
```

## Primitive

Allows executing and querying for a Primitive ADO.

| Command    | Description                       |
| ---------- | --------------------------------- |
| **create** | Creates a primitive contract.     |
| **get**    | Get the value for a given key.    |
| **set**    | Sets the value for the given key. |

### Primitive Create

Creates a primitive contract.

#### Usage

```
ado primitive create <operators>
```

Here we can specify the operators by providing comma seperated addresses.

| Flag    | Description                                                           | Usage                            |
| ------- | --------------------------------------------------------------------- | -------------------------------- |
| label   | Used to provide a label assigned to the instantiation.                | --label 'Wow what a great label' |
| admin   | Used to provide an alternative admin address for the contract.        | --admin juno1...                 |
| factory | Used to provide an alternative factory address for the instantiation. | --factory juno1...               |
| help    | Displays info about the current command.                              | --help                           |

#### Example

```
// Command
? $test@uni-3> ado primitive create juno17xc85tk5ty96slk5hdp7nf4mlwdadlqph5z5nlm0dd2tjmgsuc9s4zcyjp
- Creating your primitive...
Primitive created!

// Link to transaction details in explorer 
https://testnet.mintscan.io/juno-testnet/txs/C1FD1E094F6880861C5BA3A25AAD97F3B87FCDDECCCA2D50325064B7F51F54C7

// Address of the created contract
Address: juno14qjfl4phwgckxg0pxzj629qfhf82slff2dp292vus70e6kg2sxcsx4r53k
```

### Primitive Get

Get the value for a given key.

#### Usage

```
ado primitive get <contract address> <key>
```

#### Example

```
// Command
? $test@uni-3> ado primitive get juno144r95jrnk5erqhpuepe5mwz33e5mns7yuyhmxw7fmfx8w0duqwws9qd36x factory

//The value for the "factory" key is returned
juno1alwx0jf72y366627gavklj6rte6ygdm4kw0ctqjrr4qg4y74h84sdfct3g
```

### Primitive Set

Sets the value for the given key.

#### Usage

```
ado primitive set <contract address> <key> <value> <valuetype>
```

#### Example

```
ado primitve set juno1mm3lykw9dzvfwd7cq5hxkgnhzyheprs856v7av5uaukn3ax3p6gs3er96c test testing string
```

## Query

Queries an ADO by given contract address.

#### Usage:

```
ado query <contract-address>
```

#### Example:

```
ado query juno153xscnqm7erc847fye9p2vuqtz6465g2jr48ah5meuwzjmzlc6aqf677yd
```

You can then chose from the list of queries to run.&#x20;

## Type

Gets the type of the ADO from the contract address.

#### Usage:

```
ado type <contract-address>
```

#### Example:

<pre><code>? $main@uni-3>ado type juno153xscnqm7erc847fye9p2vuqtz6465g2jr48ah5meuwzjmzlc6aqf677yd

//Result
<strong>- Querying contract...
</strong>rates</code></pre>
