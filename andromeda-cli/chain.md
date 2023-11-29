---
description: Manage chains and configs
---

# Chain

### Available Commands

| Command                       | Description                             |
| ----------------------------- | --------------------------------------- |
| [**config**](chain.md#config) | Displays current chain config.          |
| [**copy**](chain.md#copy)     | Creates a copy of a current config.     |
| [**get**](chain.md#get)       | Displays current value for a given key. |
| [**list**](chain.md#list)     | Lists all the currently saved configs.  |
| [**new**](chain.md#new)       | Creates a new config.                   |
| [**rm**](chain.md#remove)     | Removes a config by name or chain Id.   |
| [**set**](chain.md#set)       | Sets the value for a given config key.  |
| [**use**](chain.md#use)       | Swap to a saved config.                 |

## Config

Displays the config of the chain.

| Key                           | Description                                                           |
| ----------------------------- | --------------------------------------------------------------------- |
| **chainName**                 | The name of the chain.                                                |
| **chainId**                   | The Id of the chain to use                                            |
| **chainUrl**                  | The URL of the chain to use                                           |
| **kernelAddress**             | The contract address of the Kernel of the chain.                      |
| **addressPrefix**             | The prefix for all addresses on chain                                 |
| **defualtFee**                | The default fee amount                                                |
| **blockExplorerTxPages**      | URLs to block explorers for the given chain. Must include '${txHash}' |
| **blockExplorerAddressPages** | URLs to block explorers for the given chain. Must include '${txHash}' |
| **chainType**                 | The type of chain (Testnet or Mainnet).                               |
| **iconUrl**                   | The icon for the chain.                                               |
| **name**                      | The name of the config being used.                                    |

#### Usage

```
chain config
```

#### Example

```
? $main@elgafar-1> chain config
```

The above command will generate an output similar to:

<pre><code>Current chain config
<strong>Key                       Value                                                            Description                                                          
</strong>addressPrefix             stars                                                            The prefix for all addresses on chain                                
blockExplorerTxPages      https://testnet-explorer.publicawesome.dev/stargaze/tx/${txHash} URLs to block explorers for the given chain. Must include '${txHash}'
blockExplorerAddressPages https://testnet-explorer.publicawesome.dev/stargaze/account/${a… URLs to block explorers for the given chain. Must include '${txHash}'
chainId                   elgafar-1                                                        The ID of the chain to use                                           
chainUrl                  https://rpc.elgafar-1.stargaze-apis.com/                         The URL of the chain to use                                          
chainName                 Stargaze                                                         The name of the chain                                                
chainType                 testnet                                                          The chain type                                                       
defaultFee                0.025ustars                                                      The default fee amount                                               
iconUrls                  [object Object]                                                                                                                       
kernelAddress             stars1hjm0vy35m9jhy8wjpldmaxv3yq3ctmzmpdfm9z0fscpv9zegpgqq5umjt0 The contract address of the Andromeda Kernel                         
name                      stargaze-testnet                                                 The name of the chain config used                                                                         
</code></pre>

## Copy

Creates a copy of a current config.&#x20;

#### Usage

```
chain copy <current config name/chainId> <new config name>
```

#### Example

```
? keplr-g3@galileo-3> chain copy galileo3 test
- Loading config...
Config loaded!
- Connecting client...
? keplr-g3@galileo-3> chain list
Name        Chain ID     
elgafar1    elgafar-1    
pisco1      pisco-1      
sei-network atlantic-1   
injective   injective-888
galileo3    galileo-3    
junotestnet uni-6        
test        galileo-3    
? keplr-g3@galileo-3> () 
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
? keplr-g3@galileo-3> chain list
Name        Chain ID     
elgafar1    elgafar-1    
pisco1      pisco-1      
sei-network atlantic-1   
injective   injective-888
galileo3    galileo-3    
junotestnet uni-6         
```

{% hint style="warning" %}
The chain currently is use is highlighted in green.
{% endhint %}

## New

Creates a new config.

#### Usage

```
chain new <name>
```

#### Example

```
? keplr-g3@galileo-3> chain new tutorial
```

You will be prompted to enter the required config fields. We can then query the configs:&#x20;

```
? keplr-g3@galileo-3> chain list
Name        Chain ID     
elgafar1    elgafar-1    
pisco1      pisco-1      
sei-network atlantic-1   
injective   injective-888
galileo3    galileo-3    
junotestnet uni-6 
tutorial elgafar-1
```

## Remove

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
? keplr-g3@galileo-3> chain rm tutorial
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
// Set the default fee to 0.05uandr
? user@galileo-3> chain set defaultFee 0.05uandr
```

Run `chain config` to see the changes in the config.&#x20;

## Use

Swaps to one of the saved configs.

#### Usage

```
chain use <chainId/name>
```

#### Example

```
// Get a list of chains saved. elgafar-1 is currently used. 

? guide@elgafar-1> chain list
Name        Chain ID     
elgafar1    elgafar-1    
pisco1      pisco-1      
sei-network atlantic-1   
injective   injective-888
galileo3    galileo-3    
junotestnet uni-6        
example     galileo-3      

// Set galileo-3 as the config to use using the ID.

? guide@elgafar-1> chain use galileo-3
⠂ Loading configs...
– Loading config...
Config loaded!
⠂ Connecting client...
? user@galileo-3> ()  

//Set elgafar-1 as the config to use using the name.

? user@galileo-3> chain use elgafar1
- Loading configs...
– Loading config...
Config loaded!
- Connecting client...
? guide@elgafar-1> () 
```

## CLI Configs

The default configs are saved in our GraphQL server, and can be easily fetched by anyone.

You can get all the configs from the CLI by running [chain config](chain.md#config).
