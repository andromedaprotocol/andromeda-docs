---
description: Manage Chain Config.
---

# Chain

### Available Commands

| Command    | Description                             |
| ---------- | --------------------------------------- |
| **config** | Displays current chain config.          |
| **copy**   | Creates a copy of a current config.     |
| **get**    | Displays current value for a given key. |
| **list**   | Lists all the currently saved configs.  |
| **new**    | Creates a new config.                   |
| **rm**     | Removes a config by name or chain ID.   |
| **set**    | Sets the value for a given config key.  |
| **use**    | Swap to a saved config.                 |

## Config

Displays the config of the chain.

| Key                           | Description                                                           |
| ----------------------------- | --------------------------------------------------------------------- |
| **chainName**                 | The name of the chain.                                                |
| **chainId**                   | The ID of the chain to use                                            |
| **chainUrl**                  | The URL of the chain to use                                           |
| **registryAddress**           | The contract address of the Andromeda Registry                        |
| **addressPrefix**             | The prefix for all addresses on chain                                 |
| **defualtFee**                | The default fee amount                                                |
| **blockExplorerTxPages**      | URLs to block explorers for the given chain. Must include '${txHash}' |
| **blockExplorerAddressPages** | URLs to block explorers for the given chain. Must include '${txHash}' |
| **chainType**                 | The type of chain (Testnet or Mainnet).                               |
| **iconUrl**                   | The icon for the chain.                                               |

#### Usage

```
chain config
```

#### Example

```
? $main@elgafar-1> chain config
```

The above command will generate an output similar to:

```
Current chain config

Key                       Value                                                            Description                                                          
name                      elgafar1                                                         The name of the chain config used                                    
chainName                 Stargaze                                                         The name of the chain                                                
chainId                   elgafar-1                                                        The ID of the chain to use                                           
chainUrl                  https://rpc.elgafar-1.stargaze-apis.com/                         The URL of the chain to use                                          
registryAddress           stars1n8kh8ev97f5ch2c5qtujlaelz9textg3t2tunu3emuv33d2jx9cs7mv9eq The contract address of the Andromeda Registry                       
addressPrefix             stars                                                            The prefix for all addresses on chain                                
defaultFee                0.025ustars                                                      The default fee amount                                               
blockExplorerTxPages      https://testnet-explorer.publicawesome.dev/stargaze/tx/${txHash} URLs to block explorers for the given chain. Must include '${txHash}'
blockExplorerAddressPages https://testnet-explorer.publicawesome.dev/stargaze/account/${a… URLs to block explorers for the given chain. Must include '${txHash}'
chainType                 testnet                                                          The chain type                                                       
iconUrl                   [object Object]                                                                               
```

## Copy

Creates a copy of a current config.&#x20;

#### Usage:

```
chain copy <current config name/chainId> <new config name>
```

#### Example:

```
? $main@elgafar-1> chain copy elgafar-1 test
Config loaded!
– Connecting client...

? $main@elgafar-1> chain list
Name     Chain ID 
uni3     uni-3    
elgafar1 elgafar-1
galileo2 galileo-2
pisco1   pisco-1  
test     elgafar-1
```

{% hint style="warning" %}
When you copy a config, the wallets from the copied config are also copied to the new one.&#x20;
{% endhint %}

## Get

Gets the value for the specified key. The keys are the ones found above when we call chain config (chainId, chainUrl, ect...)

#### Usage

```
chain get <key>
```

{% hint style="warning" %}
If the \<key> is not specified, then the entire config will be fetched.
{% endhint %}

#### Example

```
//Get the chainName of the chain

? $main@elgafar-1> chain get chainName
Current chain config

Key       Value    Description          
chainName Stargaze The name of the chain

```

## List

Lists all the saved configs.

#### Usage

```
chain list
```

#### Example

```
? $main@uni-3> chain list
Chain ID 
uni-3    
galileo-2
elgafar-1
```

{% hint style="warning" %}
The chain currently is use is highlighted in green.
{% endhint %}

## New

Creates a new config.

#### Usage:

```
chain new <name>
```

#### Example:

```
? $main@elgafar-1> chain new tutorial
```

You will be prompted to enter the required config fields. We can then query the configs:&#x20;

```
? $main@elgafar-1> chain list
Name     Chain ID 
uni3     uni-3    
elgafar1 elgafar-1
galileo2 galileo-2
pisco1   pisco-1  
tutorial elgafar-1
```

## Rm

Removes a config by name or chain ID.

{% hint style="warning" %}
You can only remove configs that have been added and not default configs.
{% endhint %}

#### Usage

```
chain rm <config name>
```

#### Example

```
? $main@uni-5> chain rm copyofjuno
? Select new config to use: (Use arrow keys)
❯ junotestnet 
  elgafar1 
  galileo2 
  pisco1 
```

## Set

Sets the value for a given config key.

{% hint style="warning" %}
This command is disabled on the default chain configs since they are made to be immutable. Any new chain configs that are created or copied can use the command.
{% endhint %}

#### Usage

```
chain set <key> <value>
```

#### Example

Change some of the values in the config:

```
// Set the default fee to 0.03ujunox
? $test@uni-3> chain set defaultFee 0.03ujunox
```

Run `chain config` to see the changes in the config.&#x20;

## Use

Swaps to one of the saved configs.

#### Usage

```
chain use <chainId or name>
```

#### Example

```
// Get a list of chains saved. elgafar-1 is currently used. 

? $main@elgafar-1> chain list
Name     Chain ID 
uni3     uni-3    
elgafar1 elgafar-1
galileo2 galileo-2
pisco1   pisco-1  

// Set pisco-1 as the config to use using the name.

? $main@elgafar-1> chain use pisco1
Config loaded!
- Connecting client...
? $pisco-1> () 

//Set galileo-2 as the config to use using the chainId.

? $pisco-1> chain use galileo-2
Config loaded!
– Connecting client...
? $main@galileo-2> () 
```

## CLI Configs

The default configs are hardcoded in, which allows easily switching from one chain to another. Currently there are four saved configs to use:

#### Andromeda

Config of Andromeda's galileo-2 testnet:

```typescript
{
    "name": "galileo2",
    "chainName": "Andromeda",
    "chainId": "galileo-2",
    "chainUrl": "https://rpc-andromeda-testnet.cereslabs.io/",
    "registryAddress": "andr14hj2tavq8fpesdwxxcu44rty3hh90vhujrvcmstl4zr3txmfvw9shptkql",
    "addressPrefix": "andr",
    "defaultFee": "0.025uandr",
    "blockExplorerTxPages": [
      "https://testnet-ping.wildsage.io/andromeda/tx/${txHash}"
    ],
    "blockExplorerAddressPages": [
      "https://testnet-ping.wildsage.io/andromeda/account/${address}"
    ],
    "chainType": "testnet",
    "iconUrl": {
      "sm": "https://testnet-ping.wildsage.io/logos/andromeda.png",
      "lg": "https://testnet-ping.wildsage.io/logos/andromeda.png"
    }
  }
```

#### Juno

Config of Juno's uni-5 testnet:

```typescript
  {
    "name": "junotestnet",
    "chainName": "Juno",
    "chainId": "uni-5",
    "chainUrl": "https://rpc.uni.juno.deuslabs.fi:443",
    "registryAddress": "juno133fdsnvcah870exzcyxknydswyh778jfhwxzlhhgjuagh4482zpqp856dz",
    "addressPrefix": "juno",
    "defaultFee": "0.025ujunox",
    "blockExplorerTxPages": [
      "https://testnet.mintscan.io/juno-testnet/txs/${txHash}"
    ],
    "blockExplorerAddressPages": [
      "https://testnet.mintscan.io/juno-testnet/account/${address}"
    ],
    "chainType": "testnet",
    "iconUrl": {
      "sm": "https://assets.coingecko.com/coins/images/19249/small/juno.png",
      "lg": "https://assets.coingecko.com/coins/images/19249/large/juno.png"
    }
  }
```

#### Stargaze

Config of Stargaze's elgafar-1 testnet:

```typescript
{
    "name": "elgafar1",
    "chainName": "Stargaze",
    "chainId": "elgafar-1",
    "chainUrl": "https://rpc.elgafar-1.stargaze-apis.com/",
    "registryAddress": "stars1n8kh8ev97f5ch2c5qtujlaelz9textg3t2tunu3emuv33d2jx9cs7mv9eq",
    "addressPrefix": "stars",
    "defaultFee": "0.025ustars",
    "blockExplorerTxPages": [
      "https://testnet-explorer.publicawesome.dev/stargaze/tx/${txHash}"
    ],
    "blockExplorerAddressPages": [
      "https://testnet-explorer.publicawesome.dev/stargaze/account/${address}"
    ],
    "chainType": "testnet",
    "iconUrl": {
      "sm": "https://assets.coingecko.com/coins/images/22363/small/stars.png?1645256657",
      "lg": "https://assets.coingecko.com/coins/images/22363/large/stars.png?1645256657"
    }
  }
```

#### Terra-2

Config of Terra's pisco1 testnet:

```typescript
{
    "name": "pisco1",
    "chainName": "Terra2",
    "chainId": "pisco-1",
    "chainUrl": "https://pisco-rpc.dalnim.finance/",
    "registryAddress": "terra1d29xk5fenxj2039fklmy57qcajwcpwe2lj2vspku82wh53cn39sssuzavq",
    "addressPrefix": "terra",
    "defaultFee": "0.2uluna",
    "blockExplorerTxPages": ["https://finder.terra.money/testnet/tx/${txHash}"],
    "blockExplorerAddressPages": [
      "https://finder.terra.money/testnet/account/${address}"
    ],
    "chainType": "testnet",
    "iconUrls": {
      "sm": "https://assets.coingecko.com/coins/images/25767/small/01_Luna_color.png",
      "lg": "https://assets.coingecko.com/coins/images/25767/large/01_Luna_color.png"
    }
  }
```
