---
description: Query, execute, or create and ADO
---

# ADO

### Available commands

| Command       | Description                                                             |
| ------------- | ----------------------------------------------------------------------- |
| **create**    | Creates an ADO by given type.                                           |
| **execute**   | Executes a message on an ADO by given address.                          |
| **db**        | Allows querying the on chain ADO Database.                              |
| **info**      | Queries the info of ADO for a given address.                            |
| **list**      | Queries details about your deployed apps and ADOs for the current chain |
| **modules**   | Allows management of modules for an ADO.                                |
| **operators** | Allows management of operators for an ADO.                              |
| **query**     | Queries an ADO by given address.                                        |
| **transfer**  | Transfers ownership of an ADO.                                          |

## Create

Creates and ADO based on the specified ADO type.

#### Usage

```
ado create <ado-type> 
```

#### Example

```
ado create timelock
```

We are then prompted to add the information needed to create the timelock ADO.&#x20;

#### Flags

| Flag         | Description                                                                      | Usage                                   |
| ------------ | -------------------------------------------------------------------------------- | --------------------------------------- |
| **label**    | Used to provide a label assigned to the instantiation                            | --label 'Wow what a great transaction!' |
| **admin**    | Used to provide an alternative admin address for the contract                    | -- admin juno1...                       |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simulate                             |
| **help**     | Displays info about the current command.                                         | --help                                  |

## Execute

Executes a message on an ADO by given contract address.&#x20;

#### Usage

```
ado execute <contract address> 
```

#### Example

```
ado execute juno153xscnqm7erc847fye9p2vuqtz6465g2jr48ah5meuwzjmzlc6aqf677yd
```

You can then chose from the list of executes to run.

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

## Database

Allows querying the on chain ADO DB.

| Command          | Description                                           |
| ---------------- | ----------------------------------------------------- |
| **address**      | Gets the address of the database contract being used. |
| **updatecodeid** | Updates the code ID for a given ADO in the factory.   |

### Db Address

Gets the address of the database contract being used.

#### Usage

```
ado db address
```

#### Example

```
? main@uni-5> ado db address
juno17uvgdeum5fnxns4u6r547tgsucd7zwss9tgzh8rllwvcjqmlzrzq6p50yc
? main@uni-5> () 
```

### Db Getcodeid

Fetches the code ID for a given ADO.

**Usage**

```
ado db getcodeid <ado key>
```

**Example**

```
? main@uni-5> ado db getcodeid auction
- Querying contract...
Code ID: 179
```

## Info

Queries the info of specified ADO contract address.

#### Usage

```
ado info <ADO address>
```

#### Example

```
? main@uni-5> ado info juno1a7arcy7k0ral0sgqncs9adgzwv8z6zvpfxggta32wauwpj2x8xksv42upn
 Querying ADO info...

ADO Info
Type: splitter
Version: v0.1.0
Owner: juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem
Publisher: juno1dxw33nhqz5v4ht2xu5y73y3r3epcwffqccwnnjnvkd7pl5dgxv2szumd7a
Created Height: 76940
? main@uni-5> () 
```

## List

Queries details about your deployed apps and ADOs for the current chain.

#### Usage

```
ado list
```

#### Example

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

## Modules

Allows management of modules for an ADO.

| Command  | Description                                       |
| -------- | ------------------------------------------------- |
| **add**  | Adds a module to the ADO.                         |
| **edit** | Edits an already added module attached to an ADO. |
| **list** | Lists all modules for an ADO.                     |
| **rm**   | Removes a module from an ADO.                     |

### Modules Add

Add a module to an ADO.

#### Usage

```
ado modules add <address>
```

You are then prompted to provide the module's type and the module's contract address.

#### Example

```
? main@uni-5> ado modules add juno1err4ar9c92gdrunrln6fg302m8uxs686qx5wm3affm8g633x5dvs4edwq6
⠂ Querying contract...
? Input the module type: address-list
? Input the module address: juno1a3hm3ne4fqdhtwykt0d5dzxtz88kgr6cwg9pdk4nj8agl93kqtvq5kgdq6
? Should this module be mutable? Yes
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 164852
Fee estimates:
   4122ujunox

? Do you want to proceed? Yes
- Executing Tx...

Module registered!

https://testnet.mintscan.io/juno-testnet/txs/8DA807AE77BB956D89E5B7FE19E6A2D6685AA34C4650F721ED396C34421F0308
? main@uni-5> ()
```

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

### Modules Edit

Edits an already added module attached to an ADO.

#### Usage

```
ado modules edit <address>
```

#### Example

```
? main@uni-5> ado modules edit juno1err4ar9c92gdrunrln6fg302m8uxs686qx5wm3affm8g633x5dvs4edwq6
- Querying contract...
– Querying modules...
? Choose which module to edit: (1) address-list juno1a3hm3ne4fqdhtwykt0d5dzxtz88kgr6cwg9pdk4nj8agl93kqtvq5kgdq6
? Input the module type (currently: address-list): address-list
? Input the module address (currently: juno1a3hm3ne4fqdhtwykt0d5dzxtz88kgr6cwg9pdk4nj8agl93kqtvq5kgdq6): 
juno1f2yzuc6agmkk8y0m3t05evsx475rf3tfk2xmqy8zs0xfzmvpy7kqggksl0
? Should this module be mutable? (currently: juno1a3hm3ne4fqdhtwykt0d5dzxtz88kgr6cwg9pdk4nj8agl93kqtvq5kgdq6) Yes
- Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 161077
Fee estimates:
   4027ujunox

? Do you want to proceed? Yes
- Executing Tx...

Module removed!

https://testnet.mintscan.io/juno-testnet/txs/EBE4F1D0EB50F2335602E37AD08C12DFD667057B18315DFF87BAE60B0C23BA12
? main@uni-5> () 
```

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

### Modules List

Lists all the modules of the specified ADO.

#### Usage

```
ado modules list <address>
```

#### Example

```
? main@uni-5> ado modules list juno175hu3dxvdy40snmpwhlkzaklp5m60qj4gmghhrhs7st5pjw7ca4s2x6kz6
– Querying modules...

  Type         Address   Mutable
1 address-list whitelist x      
2 rates        royalties x      
? main@uni-5> () 
```

### Module Remove

Removes a module from an ADO.

{% hint style="warning" %}
You can only remove mutable modules.
{% endhint %}

#### Usage

```
ado modules rm <address>
```

#### Example

```
? main@uni-5> ado modules rm juno1yvxl7jcuged55rjl57cd4c967p7l48avnv9r2fcgs0sshl07fuasn8u22f
⠂ Querying contract...
– Querying modules...
? Choose which module to remove: (1) address-list juno1a3hm3ne4fqdhtwykt0d5dzxtz88kgr6cwg9pdk4nj8agl93kqtvq5kgdq6
⠂ Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 144551
Fee estimates:
   3614ujunox

? Do you want to proceed? Yes
– Executing Tx...

Module removed!

https://testnet.mintscan.io/juno-testnet/txs/00C0EA38B09593155414CCB1088E0B7F7319F365A73FA0DEE1E9ECEA36D64FEC
? main@uni-5> () 
```

**Flags**

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

## Operators

Allows management of operators for an ADO.

| Command  | Description                        |
| -------- | ---------------------------------- |
| **add**  | Adds an operator to an ADO.        |
| **list** | Lists all the operators of an ADO. |
| **rm**   | Removes an operator from an ADO.   |

### Operators add&#x20;

Adds an operator to an ADO.

#### Usage

```
ado operators add <address> <new operator address>
```

#### Example

```
? main@uni-5> ado operators add juno1yvxl7jcuged55rjl57cd4c967p7l48avnv9r2fcgs0sshl07fuasn8u22f juno17ssms3dz3n88ltauh2735df6qslxttpxst0njsd8nx3x9vde5yfqyzt
l5p
⠂ Querying operators...
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 158951
Fee estimates:
   3974ujunox

? Do you want to proceed? Yes
⠂ Executing Tx...

Operator added!

https://testnet.mintscan.io/juno-testnet/txs/1163326756B4401823E7E627D13F7A859D77DBB086B1EC7DAF766650FD397B6A
? main@uni-5> () 
```

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

### Operators List

Lists all the operators of an ADO.

#### Usage

```
ado operators list <address>
```

#### Example

```
? main@uni-5> ado operators list juno1yvxl7jcuged55rjl57cd4c967p7l48avnv9r2fcgs0sshl07fuasn8u22f
⠂ Querying operators...

Operator Address                                               
juno17ssms3dz3n88ltauh2735df6qslxttpxst0njsd8nx3x9vde5yfqyztl5p
juno1err4ar9c92gdrunrln6fg302m8uxs686qx5wm3affm8g633x5dvs4edwq6
? main@uni-5> () 
```

### Operators Remove  <a href="#operators-add" id="operators-add"></a>

Removes an operator from an ADO.

**Usage**

```
ado operators rm <address> <operator address to remove>
```

**Example**

```
? main@uni-5> ado operators rm juno1yvxl7jcuged55rjl57cd4c967p7l48avnv9r2fcgs0sshl07fuasn8u22f juno17ssms3dz3n88ltauh2735df6qslxttpxst0njsd8nx3x9vde5yfqyztl5p
- Querying operators...
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 152922
Fee estimates:
   3824ujunox

? Do you want to proceed? Yes
– Executing Tx...

Operator removed!

https://testnet.mintscan.io/juno-testnet/txs/740625CD4B4D0882D843BE75DBD07288BA1FB9FBCCEB330D237E7F61B0EDF16B
? main@uni-5> () 
```

#### Flags

| Flag         | Description                                                                      | Usage                                  |
| ------------ | -------------------------------------------------------------------------------- | -------------------------------------- |
| **funds**    | Funds to send with the message                                                   | --funds 100ujunox                      |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

## Query

Queries an ADO by given contract address.

#### Usage

```
ado query <address>
```

#### Example

```
ado query juno153xscnqm7erc847fye9p2vuqtz6465g2jr48ah5meuwzjmzlc6aqf677yd
```

You can then chose from the list of queries to run.&#x20;

## Transfer

Transfers ownership of an ADO.

#### Usage

```
ado transfer <address> <new owner address>
```

#### Example

```
? main@uni-5> ado transfer juno1yvxl7jcuged55rjl57cd4c967p7l48avnv9r2fcgs0sshl07fuasn8u22f juno13nt2nfvjqgp4qdeg769qyjwzzfmppsdu2380gedqk6czmqtkq6fs8jrx7z
- Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 148616
Fee estimates:
   3716ujunox

? Do you want to proceed? Yes
— Executing Tx...

ADO Transferred!

https://testnet.mintscan.io/juno-testnet/txs/8955682FA597EAA60A5F524A0C626CBDB3519CF8EFBE733FA20896D08F538564
? main@uni-5> () 
```
