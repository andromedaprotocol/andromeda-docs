---
description: Query, execute, or create and ADO
---

# ADO

### Available commands

| Command                           | Description                                                             |
| --------------------------------- | ----------------------------------------------------------------------- |
| [**create**](ado.md#create)       | Creates an ADO by given type.                                           |
| [**execute**](ado.md#execute)     | Executes a message on an ADO by given address.                          |
| [**db**](ado.md#database)         | Allows querying the on chain ADO Database.                              |
| [**info**](ado.md#info)           | Queries the info of ADO for a given address.                            |
| [**list**](ado.md#list)           | Queries details about your deployed apps and ADOs for the current chain |
| [**modules**](ado.md#modules)     | Allows management of modules for an ADO.                                |
| [**operators**](ado.md#operators) | Allows management of operators for an ADO.                              |
| [**query**](ado.md#query)         | Queries an ADO by given address.                                        |
| [**transfer**](ado.md#transfer)   | Transfers ownership of an ADO.                                          |

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
ado execute <ado-address> 
```

#### Example

```
ado execute andr1gfddqxkhffjykm8z9wj9gym7khvazlkqx0ynsa55kd93vx4tjqws9y77pk
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

Allows querying the on chain [ADO DB](../platform-and-framework/ado-base.md).

| Command          | Description                                           |
| ---------------- | ----------------------------------------------------- |
| **address**      | Gets the address of the database contract being used. |
| **updatecodeid** | Updates the code Id for a given ADO in the factory.   |

### Db Address

Gets the address of the database contract being used.

#### Usage

```
ado db address
```

#### Example

```
? user@galileo-3> ado db address
andr1yyca08xqdgvjz0psg56z67ejh9xms6l436u8y58m82npdqqhmmtq9snx2v
? user@galileo-3> () 
```

### Db Getcodeid

Fetches the code Id for a given ADO.

**Usage**

```
ado db getcodeid <ado key>
```

**Example**

```
? user@galileo-3> ado db getcodeid auction
- Querying contract...
Code ID: 11
? user@galileo-3> () 
```

## Info

Queries the info of specified ADO contract address.

#### Usage

```
ado info <ado-address>
```

#### Example

```
? user@galileo-3> ado info andr1yyca08xqdgvjz0psg56z67ejh9xms6l436u8y58m82npdqqhmmtq9snx2v
– Querying ADO info...

ADO Info
Type: adodb
Version: v0.1.1
Owner: andr1jr7l9mw0mkqm7vdmr7vclsqcf9u06x06gcfm9x
Publisher: andr1jr7l9mw0mkqm7vdmr7vclsqcf9u06x06gcfm9x
Created Height: 11840
? user@galileo-3> () 
```

## List

Queries details about your deployed apps and ADOs for the current chain.

#### Usage

```
ado list
```

#### Example

```
 ? guides@galileo-3> ado list
⠂ Searching the Cosmos...
Address                                                         ADO Type App Contract                                                   
andr1dtaqwlmzlk3jku5un6h6rfunttmwsqnfz7evvdf4pwr0wypsl68qa30vxv cw20                                                                    
andr138gmtsqwcz0z09ja6y4cs3uyestx85440xcuwsght70jvmc9v92s9lwvj0 app                                                                     
andr1qdgvugdnscwnj8lc96q666000gyjv434kn9zl9ey3dph6p0cunuszv3dwf cw721    andr138gmtsqwcz0z09ja6y4cs3uyestx85440xcuwsght70jvmc9v92s9lwvj0
? guides@galileo-3> () 
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
ado modules add <ado-address>
```

You are then prompted to provide the module's type and the module's contract address.

#### Example

```
? guides@galileo-3> ado modules add andr1qdgvugdnscwnj8lc96q666000gyjv434kn9zl9ey3dph6p0cunuszv3dwf
— Querying contract...
? Input the module type: address-list
? Input the module address: andr1swwe3v2tezqgfwrh8wg8vxv6xgqwxtar8pn4yphfn40qdrj8fnvslhcdz7
? Should this module be mutable? No
— Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 165146
Fee estimates:
   4129uandr

? Do you want to proceed? Yes
- Executing Tx...

Module registered!

https://testnets.cosmosrun.info/andromeda-galileo-3/tx/8FA6B89EEAA8F33E767FEB9985ECFCE0B55CA51841D69568424E963B3AD4BE0E
? guides@galileo-3> () 
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
ado modules edit <ado-address>
```

#### Example

```
? keplr-g3@galileo-3> ado modules edit andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g
- Querying contract...
– Querying modules...
? Choose which module to edit: (1) address-list andr1swwe3v2tezqgfwrh8wg8vxv6xgqwxtar8pn4yphfn40qdrj8fnvslhcdz7
? Input the module type (currently: address-list): address-list
? Input the module address (currently: andr1swwe3v2tezqgfwrh8wg8vxv6xgqwxtar8pn4yphfn40qdrj8fnvslhcdz7): andr1kxdy6hhmanuas0lajj77uvsk636h8270ze62792shss62ahr56tqcm0tfh
? Should this module be mutable? (currently: andr1swwe3v2tezqgfwrh8wg8vxv6xgqwxtar8pn4yphfn40qdrj8fnvslhcdz7) Yes
- Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 161311
Fee estimates:
   4033uandr

? Do you want to proceed? Yes
— Executing Tx...

Module updated!

https://testnets.cosmosrun.info/andromeda-galileo-3/tx/FF7F3A6581A92605971109D05F7F5F739A18B8103725252409839CB93753634B
? keplr-g3@galileo-3> () 
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
ado modules list <ado-address>
```

#### Example

```
? keplr-g3@galileo-3> ado modules list andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g
– Querying modules...

  Type         Address                                                         Mutable
1 address-list andr1kxdy6hhmanuas0lajj77uvsk636h8270ze62792shss62ahr56tqcm0tfh ✓      
? keplr-g3@galileo-3> () 
```

### Module Remove

Removes a module from an ADO.

{% hint style="warning" %}
You can only remove mutable modules.
{% endhint %}

#### Usage

```
ado modules rm <ado-address>
```

#### Example

```
? keplr-g3@galileo-3> ado modules rm andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g
- Querying contract...
- Querying modules...
? Choose which module to remove: (1) address-list andr1kxdy6hhmanuas0lajj77uvsk636h8270ze62792shss62ahr56tqcm0tfh
— Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 144518
Fee estimates:
   3613uandr

? Do you want to proceed? Yes
– Executing Tx...

Module removed!

https://testnets.cosmosrun.info/andromeda-galileo-3/tx/1AF193D42F08919C0A62EBC3F4D364F6864AB4EA721AB31F8DF2754EC4A32FF2
? keplr-g3@galileo-3> () 
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
ado operators add <ado-address> <new operator address>
```

#### Example

```
? keplr-g3@galileo-3> ado operators add andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g andr105j9m29uk2486rjwawkcucp8e3l52f8wdlm8ay
- Querying operators...
— Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 147859
Fee estimates:
   3697uandr

? Do you want to proceed? Yes
- Executing Tx...

Operator added!

https://testnets.cosmosrun.info/andromeda-galileo-3/tx/3756D53D316816C3225A7BE8560A3E83D4DD933D56092C449F4717E26DC3469A
? keplr-g3@galileo-3> () 
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
ado operators list <ado-address>
```

#### Example

```
? keplr-g3@galileo-3> ado operators list andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g
- Querying operators...

Operator Address                           
andr105j9m29uk2486rjwawkcucp8e3l52f8wdlm8ay
? keplr-g3@galileo-3> () 
```

### Operators Remove  <a href="#operators-add" id="operators-add"></a>

Removes an operator from an ADO.

**Usage**

```
ado operators rm <ado-address> <operator address to remove>
```

**Example**

```
? keplr-g3@galileo-3> ado operators rm andr1pjpvwn5065jcw5qgkx3qdzvxfvkqcepnku9gmyw3mtueeq4320qqr9dc4g andr105j9m29uk2486rjwawkcucp8e3l52f8wdlm8ay
- Querying operators...
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 143173
Fee estimates:
   3580uandr

? Do you want to proceed? Yes
– Executing Tx...

Operator removed!

https://testnets.cosmosrun.info/andromeda-galileo-3/tx/2DA9C3EDD1CD620CF5C57797A9023993A48AD18A2ED682E6601B344280B75BA7
? keplr-g3@galileo-3> () 
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
ado query <ado-address>
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
