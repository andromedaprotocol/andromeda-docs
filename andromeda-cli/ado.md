---
description: Query, execute, or create and ADO
---

# ADO

### Available commands

| Command     | Description                                                             |
| ----------- | ----------------------------------------------------------------------- |
| **create**  | Creates an ADO by given type.                                           |
| **execute** | Executes a message on an ADO by given address.                          |
| **factory** | Allows executing and querying for a Factory ADO.                        |
| **list**    | Queries details about your deployed apps and ADOs for the current chain |
| **query**   | Queries an ADO by given address.                                        |
| **type**    | Queries the type of ADO for a given address.                            |

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

### Factory Getcodeid

Fetches the code ID for a given ADO.

**Usage:**

```
ado factory getcodeid <ado key>
```

**Example:**

```
? $main@elgafar-1> () ado factory getcodeid auction
```

## List

Queries details about your deployed apps and ADOs for the current chain.

#### Usage:

```
ado list
```

#### Example:

```
 $main@uni-5> ado list
⠂ Searching the Cosmos...
Address                                                         ADO Type      App Contract                                                   
juno1xg3c9lx8554s44umj6f9x4tknn9yyv8v0yfwys4y4l4xgdjc6fysl374p2 app                                                                          
juno12rldgl4x4clvkgmw6npjy5htupp24gercczjsr3d97hcte3vsj0q4fvs63 app                                                                          
juno1fehkdr6w47w35ux6npv3hqhhctt9fcllxt4ccswj53800n4c80gqjrnu0d app                                                                          
juno1dxw33nhqz5v4ht2xu5y73y3r3epcwffqccwnnjnvkd7pl5dgxv2szumd7a app                                                                          
juno1fjd5qr83hdkfayqaejphp2vvjacw3fye6gpl79jp5zhujtr5m3gsxgp4c8 crowdfund     juno1fjd5qr83hdkfayqaejphp2vvjacw3fye6gpl79jp5zhujtr5m3gsxgp4c8
juno14g7rcqe0vwsf8pgaz2dg77p5mm08hvhwwfu73653406rhzh3rdgqckhq5g cw721         juno14g7rcqe0vwsf8pgaz2dg77p5mm08hvhwwfu73653406rhzh3rdgqckhq5g
juno1khsspglsfgkhxpsyfkzd3a2cdf7d8mngpvt22m4y0uznz24mjesq3kzawz vault         juno1khsspglsfgkhxpsyfkzd3a2cdf7d8mngpvt22m4y0uznz24mjesq3kzawz
juno1w23ad8kkfk7g7gc5cgce9c6lwnvvn4kur7ycxjkt3kfdngjkyvyskajhur vault         juno1w23ad8kkfk7g7gc5cgce9c6lwnvvn4kur7ycxjkt3kfdngjkyvyskajhur
juno1a7arcy7k0ral0sgqncs9adgzwv8z6zvpfxggta32wauwpj2x8xksv42upn splitter    
? $main@uni-5> () 
```

#### Flags

| Flag     | Description                             |              |
| -------- | --------------------------------------- | ------------ |
| **type** | Filter assets by ADO type               | --type cw721 |
| **help** | Displays info about the current command | --help       |

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
