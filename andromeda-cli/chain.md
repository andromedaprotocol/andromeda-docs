---
description: Manage Chain Config.
---

# Chain

### Available Commands

| Command    | Description                             |
| ---------- | --------------------------------------- |
| **config** | Displays current chain config.          |
| **get**    | Displays current value for a given key. |
| **list**   | Lists all the currently saved configs.  |
| **new**    | Creates a new config.                   |
| **copy**   | Creates a copy of a current config.     |
| **rm**     | Removes a config by name or chain ID.   |
| **set**    | Sets the value for a given config key.  |
| **use**    | Swap to a saved config.                 |

## Chain Config

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

## Chain Get

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

## Chain List

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

## Chain New

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

## Chain Copy

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

## Chain Set

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

## Chain Use

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
