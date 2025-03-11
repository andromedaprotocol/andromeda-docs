# Kernel

## Introduction&#x20;

The **Andromeda Kernel** acts as the core of the operating system.&#x20;

The Kernel receives and handles packets from ADOs to be relayed to a specified **recipient.** The Kernel keeps track of the original sender of the message. It also verifies that the packet is sent by an Andromeda certified ADO before relaying the message.&#x20;

The Kernel is also responsible for:

* &#x20;Relaying any IBC messages across any two chains that have an Andromeda Kernel deployed and a channel set up.
* Keeping track of the other AMP ADOs such as the ADODB, VFS, and Economics.

All of our ADOs have an [AMPReceive](../ado-base/#ampreceive) execute message to handle receiving packets from the Kernel.

{% hint style="warning" %}
The Kernels are set up by the Andromeda team on each chain. This means that as a regular user, you will not need to interact with this ADO yourself in most cases. This page serves as additional information on how it works for people that are interested into getting a deep understanding of our system.&#x20;

The only only message that users will be calling is the [Recover](kernel.md#recover) execute message that will recover IBC funds sent in case the IBC message fails.
{% endhint %}

## InstantiateMsg

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct InstantiateMsg {
    pub owner: Option<String>,
    pub chain_name: String
}
```
{% endtab %}
{% endtabs %}

| Name         | Type            | Description                                                                                                 |
| ------------ | --------------- | ----------------------------------------------------------------------------------------------------------- |
| `owner`      | Option\<String> | Optional address to specify as the owner of the ADO being created. Defaults to the sender if not specified. |
| `chain_name` | String          | The name of the chain we are deploying this kernel for.                                                     |

## ExecuteMsg

### AMPReceive

Receives an AMP packet from another ADO.  The packet is received, unpacked and the messages are relayed to their intended destination.&#x20;

{% hint style="warning" %}
Only an AMPPkt sent from one of the Andromeda ADOs is accepted.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    AMPReceive(AMPPkt),
    }
```
{% endtab %}
{% endtabs %}

Information about the `AMPPkt` struct can be found [here](./#amppkt).

### Send

Constructs an AMPPkt with a given AMPMsg and sends it to the recipient.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
       Send {
        message: AMPMsg,
    },
}
```
{% endtab %}
{% endtabs %}

| Name      | Type                | Description          |
| --------- | ------------------- | -------------------- |
| `message` | [AMPMsg](./#ampmsg) | The message to send. |

### Create

Creates an ADO with the given type and message.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
Create {
        ado_type: String,
        msg: Binary,
        owner: Option<AndrAddr>,
        chain: Option<String>,
    },
 }
```
{% endtab %}
{% endtabs %}

### UpsertKeyAddress

Used to save the addresses of the AMP ADOs that the Kernel will be interacting with such as the [ADODB](andromeda-factory.md), [Economics](economics-engine.md), and [VFS](virtual-file-system.md).&#x20;

{% hint style="warning" %}
Only available to the owner of the Kernel.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    UpsertKeyAddress {
        key: String,
        value: String,
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                                               |
| ------- | ------ | --------------------------------------------------------- |
| `key`   | String | The name of the AMP ADO we want to save the address for.  |
| `value` | String | The address of the ADO.                                   |

### AssignChannels

Assigns a given channel between the current chain and the specified target `chain`. For a successfull channel to be set up, both the channel ids need to be specified.&#x20;

{% hint style="warning" %}
Only available to the owner of the Kernel.

Calling this message again, will override any previously set channels.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 AssignChannels {
        ics20_channel_id: Option<String>,
        direct_channel_id: Option<String>,
        chain: String,
        kernel_address: String,
    },
  }
```
{% endtab %}
{% endtabs %}

| Name                | Type            | Description                             |
| ------------------- | --------------- | --------------------------------------- |
| `isc20_channel_id`  | Option\<String> | The id of the ics20 channel.            |
| `direct_channel_id` | Option\<String> | The direct channel id.                  |
| `chain`             | String          | The name of the target chain.           |
| `kernel_address`    | String          | The Kernel address of the target chain. |

### Recover

Recovers sent IBC funds to the user in case an IBC message fails.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
    Recover {},
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"recover":{}
}
```
{% endtab %}
{% endtabs %}

### UpdateChainName

Update the name of the chain the kernel is deployed on. This name is initially specified at instantiation.

{% hint style="warning" %}
Only available to the owner of the Kernel ADO.&#x20;
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
   UpdateChainName {
        chain_name: String,
    },
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"update_chain_name":{
    "chain_name":"andromeda"
    }
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description                                                   |
| ------------ | ------ | ------------------------------------------------------------- |
| `chain_name` | String | The new assigned name of the chain the kernel is deployed on. |

### RegisterUserCrossChain

Used to register a VFS username cross-chain.

{% hint style="warning" %}
This message can only be called by the [VFS](virtual-file-system.md).
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum ExecuteMsg {
 RegisterUserCrossChain {
        username: String,
        address: String,
        chain: String,
    },
  }
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description                                             |
| ---------- | ------ | ------------------------------------------------------- |
| `username` | String | The username to be registerd.                           |
| `address`  | String | The address on the other chain to save the username to. |
| `chain`    | String | The name of the second chain.                           |

### Ownership

The set of ownerhsip messages. These messages are the same as the ones found in the [ADO base section](../ado-base/andromedamsg.md#ownership).

## QueryMsg

### KeyAddress

Queries the address of the specified `key`.&#x20;

{% hint style="warning" %}
Currently there are three keys that can be used:\
vfs : Gets the address of the [VFS](virtual-file-system.md) ADO used by the kernel.

adodb: Gets the address of the [ADODB](andromeda-factory.md) ADO used by the kernel.

economics: Gets the address of the [Economics](economics-engine.md) ADO used by the kernel.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{  
   #[returns(Addr)]
    KeyAddress { key: String }
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"key_address":{
    "key":"adodb"
    }
}
```
{% endtab %}
{% endtabs %}

| Name  | Type   | Description                      |
| ----- | ------ | -------------------------------- |
| `key` | String | The key to get the address for.  |

Returns the address in a string.

### VerifyAddress

Checks if the specified `address` was created by an Andromeda ADO by checking and comparing the `code_Id` of the address with the code\_Ids stored in the [ADODB](andromeda-factory.md).

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg{
    #[returns(bool)]
    VerifyAddress { address: String },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"verify_address":{
    "address":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description           |
| --------- | ------ | --------------------- |
| `address` | String | The address to check. |

Returns true if the address was created by an Andromeda ADO and false otherwise.

### ChannelInfo

Gets the saved channel information such as channel ids for the specified chain.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum QueryMsg {
<strong>    #[returns(Option&#x3C;ChannelInfoResponse>)]
</strong>    ChannelInfo { chain: String }
    }
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"channel_info":{
    "chain":"juno"
    }
}
```
{% endtab %}
{% endtabs %}

| Name    | Type   | Description                                               |
| ------- | ------ | --------------------------------------------------------- |
| `chain` | String | The name of the chain to get the channel information for. |

#### ChannelInfoResponse

```rust
pub struct ChannelInfoResponse {
    pub ics20: Option<String>,
    pub direct: Option<String>,
    pub kernel_address: String,
    pub supported_modules: Vec<String>,
}
```

| Name                | Type            | Description                                         |
| ------------------- | --------------- | --------------------------------------------------- |
| `ics20`             | Option\<String> | The ics20 id.                                       |
| `direct`            | Option\<String> | The direct channel id.                              |
| `kernel_address`    | String          | The kernel address of the specifed chain.           |
| `supported_modules` | Vec\<String>    | A list of supported modules by the specified chain. |

### Recoveries

Gets any fund recoveries available for the specified address.

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust"><strong>pub enum QueryMsg {   
</strong><strong>    #[returns(Vec&#x3C;::cosmwasm_std::Coin>)]
</strong>    Recoveries { addr: Addr },
    }
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
```json
{
"recoveries":{
    "addr":"andr1..."
    }
}
```
{% endtab %}
{% endtabs %}

| Name   | Type | Description                          |
| ------ | ---- | ------------------------------------ |
| `addr` | Addr | The address to check recoveries for. |

Returns a Vec<[Coin](../common-types.md#coin)> containing the recoverable funds.

### ChainName

Returns the saved chain name specified at instantiation.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum QueryMsg {
    #[returns(ChainNameResponse)]
    ChainName {},
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"chain_name":{}
}
```
{% endtab %}
{% endtabs %}

#### ChainNameResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ChainNameResponse {
    pub chain_name: String,
}
```
{% endtab %}

{% tab title="undefined" %}
```json
{
"chain_name:"andr1..."
}
```
{% endtab %}
{% endtabs %}

| Name         | Type   | Description                                      |
| ------------ | ------ | ------------------------------------------------ |
| `chain_name` | String | The name of the chain the kernel is deployed on. |

### ChainNameByChannel

Gets the chain name assosiated with the specified channel Id.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
   #[returns(Option<String>)]
    ChainNameByChannel { channel: String },
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"chain_name_by_channel":{
    "channel":"channel-2"
    }
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Description                             |
| --------- | ------ | --------------------------------------- |
| `channel` | String | The channel id of the channel to query. |

Returns the chain name as a string.

### PendingPackets

Queries information on any pending packets on the specified channel.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
    #[returns(PendingPacketResponse)]
    PendingPackets { channel_id: Option<String> },
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"pending_packets":{
    "channel_id":"channel-4",
    }
}
```
{% endtab %}
{% endtabs %}

### PendingPacketsResponse

```rust
pub struct PendingPacketResponse {
    pub packets: Vec<PacketInfoAndSequence>,
}

pub struct PacketInfoAndSequence {
    pub packet_info: Ics20PacketInfo,
    pub sequence: u64,
}


pub struct Ics20PacketInfo {
    pub sender: String,
    pub recipient: AndrAddr,
    pub message: Binary,
    pub funds: Coin,
    pub channel: String,
    pub pending: bool,
}
```

| Name        | Type                                    | Description                                |
| ----------- | --------------------------------------- | ------------------------------------------ |
| `sender`    | Addr                                    | The address to check recovery funds for.   |
| `recipient` | [AndrAddr](../common-types.md#andraddr) | The address receiving the packet.          |
| `message`   | Binary                                  | The attached message in the packet.        |
| `funds`     | [Coin](../common-types.md#coin)         | Any funds attached with the packet.        |
| `channel`   | String                                  | The channel being used to send the packet. |
| `pending`   | bool                                    | Whether this packet has been processed.    |

### GetEnv

Queries the value for the specified env variable.

{% hint style="warning" %}
The variable name must be uppercase and can only contain letters, numbers, and underscores.
{% endhint %}

{% tabs %}
{% tab title="Rust" %}
```rust
enum AndromedaQuery {
 #[returns(EnvResponse)]
    GetEnv { variable: String },
    }
```
{% endtab %}

{% tab title="JSON" %}
```rust
{
"get_env":{
    "variable":"ENV_3"
    }
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description        |
| ---------- | ------ | ------------------ |
| `variable` | String | The variable name. |

Returns the value in a String.&#x20;

### Recoveries

Queries any recovery funds (funds returned for a failed tx) for the specified address.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
 #[returns(Vec<::cosmwasm_std::Coin>)]
    Recoveries { addr: Addr },
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version":{}
}
```
{% endtab %}
{% endtabs %}

| Name   | Type | Description                              |
| ------ | ---- | ---------------------------------------- |
| `addr` | Addr | The address to check recovery funds for. |

Returns the denom and amounts of the recovery funds in a [Coin](../common-types.md#coin) struct.

### Version

Queries the version of the ADO.&#x20;

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery {
     #[returns(VersionResponse)]
     Version {}
     }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version":{}
}
```
{% endtab %}
{% endtabs %}

#### VersionResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct VersionResponse {
    pub version: String,
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"version": "0.1.0"
}
```
{% endtab %}
{% endtabs %}

| Name      | Type   | Descripton               |
| --------- | ------ | ------------------------ |
| `version` | String | The version of the ADO.  |

### Owner

Queries the owner of the contract.

{% tabs %}
{% tab title="Rust" %}
```rust
pub enum AndromedaQuery{
    #[returns(ContractOwnerResponse)]
    Owner{}
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
  "owner":{}
  }
}
```
{% endtab %}
{% endtabs %}

#### ContractOwnerResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct ContractOwnerResponse {
    pub owner: String
}
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"owner":"andr1..."
}
```
{% endtab %}
{% endtabs %}

### Type

Queries the ADO type.&#x20;

{% tabs %}
{% tab title="Rust" %}
<pre class="language-rust"><code class="lang-rust">pub enum AndromedaQuery {
<strong>    #[returns(TypeResponse)]
</strong>    Type {}
}
</code></pre>
{% endtab %}

{% tab title="JSON" %}
<pre class="language-json"><code class="lang-json">{
<strong>"type":{}
</strong>}
</code></pre>
{% endtab %}
{% endtabs %}

#### TypeResponse

{% tabs %}
{% tab title="Rust" %}
```rust
pub struct TypeResponse {
    pub ado_type: String,
    }
```
{% endtab %}

{% tab title="JSON" %}
```json
{
"ado_type":"auction"
}
```
{% endtab %}
{% endtabs %}

| Name       | Type   | Description          |
| ---------- | ------ | -------------------- |
| `ado_type` | String | The type of the ado. |
