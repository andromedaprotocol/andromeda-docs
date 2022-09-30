---
description: GraphQL server custom built by Andromeda to query from our Apps.
---

# Gql

### Available Commands

| Command    | Description                                                              |
| ---------- | ------------------------------------------------------------------------ |
| **app**    | Queries details about an app.                                            |
| **assets** | Queries details about your deployed apps and ADOs for the current chain. |

## App

Queries details about an app.

#### Usage

```
gql app <contract address>
```

#### Example

```
gql app juno16k0hp6eetdrus3lga005ug0vpt4cz88nwjpmfr30szfs578p46nq90j5eh
```

#### Response

The response includes the owner address, app name, and components of the app.

```
Searching the Cosmos...
Owner: juno1zkpthqsz3ud97fm6p4kxcra8ae99jgzauugyem
App Name: example_app

Components
crowdfund     crowdfund juno13u5rdsafnr6z9sj0xdkyspg99k2vvu7afj275k4zpr4huh0yqqaq2wegdu
reserve_vault vault     juno1mcmld8th8yyjf2l8aj3hpzzjyuq73fseakp5n8jhwkfkcdgnyutsqul7uj
sale_splitter splitter  juno1a697d7fy5wwyuvjc0cexhpc63jvuzk2r58y9p70e45esvalvfjtsyr2y5x
tokens        cw721     juno1yhdqlzav3zpseur7xyhcwmx7tmru7d0phvyxc3zalaaufja6sh3szzdftx
yield_vault   vault     juno1ju7fn05hscckhtd5q9dutdgpp09s9403le080agnm6m8e6uaywcsrurk08
```

### Assets

Queries details about your deployed apps and ADOs for the current chain.

#### Usage

```
gql assets
```

#### Example

```
? $main@uni-5> gql assets 
- Searching the Cosmos...
Address                                                         ADO Type      App Contract                                                   
juno1xg3c9lx8554s44umj6f9x4tknn9yyv8v0yfwys4y4l4xgdjc6fysl374p2 app                                                                                                                                                
juno1dxw33nhqz5v4ht2xu5y73y3r3epcwffqccwnnjnvkd7pl5dgxv2szumd7a app                                                                          
juno1fjd5qr83hdkfayqaejphp2vvjacw3fye6gpl79jp5zhujtr5m3gsxgp4c8 crowdfund     juno1fjd5qr83hdkfayqaejphp2vvjacw3fye6gpl79jp5zhujtr5m3gsxgp4c8
juno14g7rcqe0vwsf8pgaz2dg77p5mm08hvhwwfu73653406rhzh3rdgqckhq5g cw721         juno14g7rcqe0vwsf8pgaz2dg77p5mm08hvhwwfu73653406rhzh3rdgqckhq5g
juno1khsspglsfgkhxpsyfkzd3a2cdf7d8mngpvt22m4y0uznz24mjesq3kzawz vault         juno1khsspglsfgkhxpsyfkzd3a2cdf7d8mngpvt22m4y0uznz24mjesq3kzawz
juno1w23ad8kkfk7g7gc5cgce9c6lwnvvn4kur7ycxjkt3kfdngjkyvyskajhur vault         juno1w23ad8kkfk7g7gc5cgce9c6lwnvvn4kur7ycxjkt3kfdngjkyvyskajhur
juno1a7arcy7k0ral0sgqncs9adgzwv8z6zvpfxggta32wauwpj2x8xksv42upn splitter      juno1a7arcy7k0ral0sgqncs9adgzwv8z6zvpfxggta32wauwpj2x8xksv42upn
     
```

#### Flags

| Flag     | Description                              | Usage        |
| -------- | ---------------------------------------- | ------------ |
| **type** | Filter assets by ADO type.               | --type cw721 |
| **help** | Displays info about the current command. | --help       |
