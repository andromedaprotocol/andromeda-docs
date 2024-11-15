---
description: Manage CLI environments.
---

# Env

### Available Commands

| Command                     | Description                            |
| --------------------------- | -------------------------------------- |
| [**create**](env.md#create) | Create a new environment.              |
| [**list**](env.md#list)     | List all available environments.       |
| [**remove**](env.md#remove) | Deletes one of the environments.       |
| [**rename**](env.md#rename) | Renames an environment.                |
| [**print**](env.md#print)   | Print details about the connected env. |
| [**update**](env.md#update) | Update one of the env configurations.  |
| [**use**](env.md#use)       | Select which env to use.               |

## Create

Creates a new custom environment.&#x20;

**Usage**

```
env create 
```

**Example**

<pre><code><strong>// create new env
</strong><strong>? @galileo-4> env create
</strong>? Env Name:  example-env
? GQL Url:  https://api.andromedaprotocol.io/graphql/testnet
? Schema Url:  (https://api.andromedaprotocol.io/v1/schema) 

// list the available envs now
? @galileo-4> env list
Name        GQL                                              Schema Url                                
devnet      https://api.andromedaprotocol.io/graphql/dev     https://api.andromedaprotocol.io/v1/schema
example-env https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema
mainnet     https://api.andromedaprotocol.io/graphql/mainnet https://api.andromedaprotocol.io/v1/schema
testnet     https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema
</code></pre>

## List

Lists the available environments.

**Usage**

```
env list
```

**Example**&#x20;

```
? guides@galileo-4> env list

Name    GQL                                              Schema Url                                
devnet  https://api.andromedaprotocol.io/graphql/dev     https://api.andromedaprotocol.io/v1/schema
mainnet https://api.andromedaprotocol.io/graphql/mainnet https://api.andromedaprotocol.io/v1/schema
testnet https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema
```

## Remove

Deletes one of the environments.

{% hint style="warning" %}
You cannot remove the env that you are currently using.
{% endhint %}

**Usage**

```
env remover <env-name>
```

**Example**

```
// list envs
? guides@galileo-4> env list

Name     GQL                                              Schema Url                                
devnet   https://api.andromedaprotocol.io/graphql/dev     https://api.andromedaprotocol.io/v1/schema
mainnnet https://api.andromedaprotocol.io/graphql/mainnet https://api.andromedaprotocol.io/v1/schema
testnet  https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema

// Remove one 
? guides@galileo-4> env remove mainnet
? This action is irreversible! Are you sure you want to remove env mainnet? Yes
Env removed!

// list envs again

? guides@galileo-4> env list
Name    GQL                                              Schema Url                                
devnet  https://api.andromedaprotocol.io/graphql/dev     https://api.andromedaprotocol.io/v1/schema
testnet https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema

```

## Rename

Rename an existing environment.

**Usage**

```
env rename <old-name> <new-name>
```

**Example**

<pre><code><strong>// rename the env
</strong><strong>? guides@galileo-4> () env rename testnet testnet-renamed
</strong><strong>
</strong><strong>// List the envs
</strong>? guides@galileo-4> env list

Name            GQL                                              Schema Url                                
devnet          https://api.andromedaprotocol.io/graphql/dev     https://api.andromedaprotocol.io/v1/schema
testnet-renamed https://api.andromedaprotocol.io/graphql/testnet https://api.andromedaprotocol.io/v1/schema
</code></pre>

## Print

Prints details about the connected environment.

**Usage**

```
env print
```

**Example**

```
? cli-testing@galileo-4> env print 
Current env config

name   testnet                                          CLI Env name                   
gql    https://api.andromedaprotocol.io/graphql/testnet Graphql url for this enviroment
schema https://api.andromedaprotocol.io/v1/schema       Schema url for this enviroment 
envs   testnet,mainnet,devnet,legacy                    All available envs             

? cli-testing@galileo-4> () 
```

## Update

Update one of the configurations for the env.

**Usage**&#x20;

```
env update <field> <new value>
```

**Example**

```
env update gql https://api.andromedaprotocol.io/graphql/testnet
```

This will update the gql url used to the one specified.

## Use

Select which env to use.

**Usage**

```
env use <env-name>
```

**Example**

{% hint style="warning" %}
You can run "env use" and you will get a list of the available environments to select from.
{% endhint %}

```
? cli-testing@galileo-4> () env use mainnet
```

This will switch to use mainnet env.&#x20;
