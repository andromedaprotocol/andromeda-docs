---
description: Commands to interact with the OS ADOs.
---

# OS

### Available Commands <a href="#available-commands" id="available-commands"></a>

| Command                        | Description                               |
| ------------------------------ | ----------------------------------------- |
| [**address**](os.md#address)   | Gets the current address for Kernel.      |
| [**adodb**](os.md#adodb)       | Allows interacting with the ADO database. |
| [**execute**](os.md#execute-1) | Executes a message on the kernel.         |
| [**key**](os.md#key)           | Query an address used by the kernel.      |
| [**query**](os.md#query-1)     | Query on the Kernel itself.               |
| [**vfs**](os.md#vfs)           | Allows interacting with the VFS ADO.      |

## Address

Returns the address of the kernel used by the chain.

#### Usage

```
os address
```

#### Example

```
? mainnet-1@andromeda-1> os address
andr1jedn7tuc76u6e8csy74uts5wy2yf9d0mmp8xsjnn23ju8qj3cuyqxzj45u
? mainnet-1@andromeda-1> () 
```

## ADODB

### Address

Gets the address of the deployed ADO database for the chain.

#### Usage

```
os adodb address
```

#### Example

```
? mainnet-1@andromeda-1> os adodb address
andr1rpmtuzssxzm8w5gg79umx6d9xzw456gvdmhxm7cmyp2z95ev3n8s8n0sjm
? mainnet-1@andromeda-1> ()
```

### Execute

Execute a message on the adodb.

{% hint style="warning" %}
Most execute messages on the ADODB are restricted to the owner.&#x20;
{% endhint %}

#### Usage

```
os adodb execute
```

#### Example

```
? mainnet-1@andromeda-1> os adodb execute
– Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Publish 
  [Option 2] Unpublish 
  [Option 3] Update Action Fees 
  [Option 4] Remove Action Fees 
  [Option 5] Update Publisher 
  [Option 6] Ownership 
  exit 
```

Then select the message you want to execute.

### GetCodeId

Returns the code id used for the specified ADO type.

#### Usage

```
os adodb getcodeid <ado-type>
```

#### Example

```
? mainnet-1@andromeda-1> os adodb getcodeid auction
- Querying contract...
Code ID: 18
? mainnet-1@andromeda-1> ()
```

### Query

Perform a query on the adodb.

#### Usage

```
os adodb query
```

#### Example

```
? mainnet-1@andromeda-1> os adodb query 
– Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Code Id 
  [Option 2] Is Unpublished Code Id 
  [Option 3] Ado Type 
  [Option 4] All Ado Types 
  [Option 5] Ado Versions 
  [Option 6] Ado Metadata 
  [Option 7] Action Fee
  .
  .
  . 
```

Then select the query you want to perform.

***

## Execute

Perform and execute message on the Kernel used by the chain.

{% hint style="warning" %}
Most execute messages on the Kernel are restricted to the owner of the ADO.
{% endhint %}

#### Usage

```
os execute
```

#### Example

```
? mainnet-1@andromeda-1> os execute
— Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Amp Receive 
  [Option 2] Send 
  [Option 3] Upsert Key Address 
  [Option 4] Create 
  [Option 5] Assign Channels 
  [Option 6] Recover 
  [Option 7] Update Chain Name 
  .
  .
  .
```

Then select the execute message you want to perform.

## Key

Queries the address associated to the provided key from the Kernel. This is used to get the OS ADO addresses associated to the kernel.

{% hint style="warning" %}
The keys that can be used are "vfs", "adodb", and "economics".
{% endhint %}

#### Usage

```
os key <key-name>
```

#### Example

```
? mainnet-1@andromeda-1> os key vfs
- Querying contract...
Key Address: andr1nkkf9hrqf5rj6424nald80les69f5gvcamsfpzpyjkley534k6xqzvzq20
? mainnet-1@andromeda-1> os key adodb
— Querying contract...
Key Address: andr1rpmtuzssxzm8w5gg79umx6d9xzw456gvdmhxm7cmyp2z95ev3n8s8n0sjm
? mainnet-1@andromeda-1> os key economics
- Querying contract...
Key Address: andr1srgtnk0v8qkuut9j6k3vv4aclw78r2nrkuv7rx7fetu4305ad27sv87j6f
? mainnet-1@andromeda-1> () 
```

## Query

Perform a query on the kernel.&#x20;

#### Usage

```
os query
```

#### Example

```
? mainnet-1@andromeda-1> os query 
⠂ Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Key Address 
  [Option 2] Verify Address 
  [Option 3] Channel Info 
  [Option 4] Recoveries 
  [Option 5] Chain Name 
  [Option 6] Version 
  [Option 7] Type 
  .
  .
  .
```

Then select the query you want to perform.

## VFS

Interact with the VFS ADO used by the connected chain.

### Address

Gets the address of the deployed VFS for the chain.

#### Usage

```
os vfs address
```

#### Example

```
? mainnet-1@andromeda-1> os vfs address
andr1nkkf9hrqf5rj6424nald80les69f5gvcamsfpzpyjkley534k6xqzvzq20
? mainnet-1@andromeda-1> () 
```

### Execute

Execute a message on the VFS.

#### Usage

```
os vfs execute
```

#### Example

```
? mainnet-1@andromeda-1> os vfs execute
– Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Add Path 
  [Option 2] Add Symlink 
  [Option 3] Add Child 
  [Option 4] Register User 
  [Option 5] Register Library 
  [Option 6] Register User Cross Chain 
  [Option 7] Ownership 
```

Then select the message you want to execute.

### Paths

Queries the paths that resolve to the specified address.

#### Usage

```
os vfs paths <address>
```

#### Example

```
? mainnet-1@andromeda-1> os vfs paths andr1r2w788v9c5kfe9slwmtlumew9adylw87avyuw7tmqvtvn3563juqyf6w2q
- Querying contract...
Paths:
andr132625r5cazyenhrqqqt5whdghpf2cufxqtpvfu/auction_example/auction-tokens
? mainnet-1@andromeda-1> ()   
```

### Query

Perform a query on the VFS.

#### Usage

```
os vfs query
```

#### Example

```
? mainnet-1@andromeda-1> os vfs query
- Fetching schema...
? Select a message type: (Use arrow keys)
❯ [Option 1] Resolve Path 
  [Option 2] Sub Dir 
  [Option 3] Paths 
  [Option 4] Get Username 
  [Option 5] Get Library 
  [Option 6] Resolve Symlink 
  [Option 7] Version 
```

Then select the query you want to perform.
