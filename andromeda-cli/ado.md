---
description: Query, execute, or create and ADO
---

# ADO

### Available commands

| Command                         | Description                                                             |
| ------------------------------- | ----------------------------------------------------------------------- |
| [**create**](ado.md#create)     | Creates an ADO by given type.                                           |
| [**execute**](ado.md#execute)   | Executes a message on an ADO by given address.                          |
| [**info**](ado.md#info)         | Queries the info of ADO for a given address.                            |
| [**list**](ado.md#list)         | Queries details about your deployed apps and ADOs for the current chain |
| [**query**](ado.md#query)       | Queries an ADO by given address.                                        |
| [**transfer**](ado.md#transfer) | Transfers ownership of an ADO.                                          |

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
| **admin**    | Used to provide an alternative admin address for the contract                    | -- admin andr1...                       |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simulate                             |
| **print**    | Prints the constructed message before simulating.                                | --print                                 |
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
| **funds**    | Funds to send with the message                                                   | --funds 100uandr                       |
| **memo**     | An optional memo to attach to the message                                        | --memo 'Wow what a great transaction!' |
| **simulate** | Simulates the transaction without broadcasting it. Useful to estimate gas costs. | -- simmulate                           |
| **print**    | Prints the constructed message before simulating.                                | --print                                |
| **help**     | Displays info about the current command.                                         | --help                                 |

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

| Flag       | Description                             | Usage         |
| ---------- | --------------------------------------- | ------------- |
| **type**   | Filter assets by ADO type               | --type cw721  |
| **search** | Filter assets by ADO name               | --search nfts |
| **help**   | Displays info about the current command | --help        |

## Query

Queries an ADO by given contract address.

#### Usage

```
ado query <ado-address>
```

#### Example

```
ado query andr1yyca08xqdgvjz0psg56z67ejh9xms6l436u8y58m82npdqqhmmtq9snx2v
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
? guide@elgafar-1> ado transfer stars1z77gc0w0h2tly8kkc7y8mt3p5w7q2tev9qe9y3skaq7l6s4xfa8st6793k stars105j9m29uk2486rjwawkcucp8e3l52f8wpgyc2c
? Would you like to add funds to this message? No
– Simulating Tx...
Transaction simulated!

Cost Estimates
Gas Used: 131128
Fee estimates:
   3279ustars

? Do you want to proceed? Yes
- Executing Tx...

ADO Transferred!

https://testnet-explorer.publicawesome.dev/stargaze/tx/042353250E0D6C67C320195032CB0BB7EDE1873F2434426A9812551ED4206E3E
? guide@elgafar-1> () 
```
