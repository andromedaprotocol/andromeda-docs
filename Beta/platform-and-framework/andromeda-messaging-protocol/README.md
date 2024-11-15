# Andromeda Messaging Protocol

The **Andromeda Messaging Protoco**l (AMP) is an advanced messaging system tailored for Andromeda ADOs. It has been designed to optimize communication between ADOs, addressing common issues present in existing CosmWasm messaging protocols.

All ADO communication can be done through AMP. This means that ADOs would send their messages to AMP (The kernel)  which relays it to the intended ADO. The AMP layer also interacts with the rest of the OS systems such as the [Virtual File System](virtual-file-system.md) (VFS), [economics engine](economics-engine.md), [ADO database](andromeda-factory.md).



<figure><img src="../../.gitbook/assets/Stand-alone Kernel Diagram.jpg" alt=""><figcaption></figcaption></figure>

This messaging system is primarily handled by our [Kernel](kernel.md) ADO and is aimed to solve the following problems:&#x20;

{% hint style="warning" %}
Although not recommended, we have kept the option of sending messages directly from one ADO to another without passing through the AMP layer.&#x20;
{% endhint %}

### Message Tracking

As you may know, standard messaging protocols on CosmWasm are limited when it comes to providing traceability of messages. This means that if contract A wants to send a message to contract C through one or more intermediate contracts, such as A->B->C, contract C has no way of knowing the original sender of the message, which in this case is A. This can lead to complications in verifying the origin of messages, and can make it difficult to accurately track the path a message has taken.

AMP takes care of this by keeping a record of the previous and original sender of a message.&#x20;

### Security

The Andromeda Messaging Protocol has been designed to enhance the security of the system, providing an extra layer of verification to ensure that messages are only relayed through AMP by authenticated, Andromeda-certified ADOs. This guarantee helps protect the system from potentially malicious contracts, while allowing contracts instantiated using a certified ADO to securely and reliably send and receive messages.

{% hint style="warning" %}
The [Andromeda ADODB](andromeda-factory.md) keeps a mapping of ADOs and code Ids. To be a certified Andromeda ADO and interact with AMP, an ADO needs to be instantiated by one of the code Ids found in the ADODB.&#x20;
{% endhint %}

### Additional Control&#x20;

Our AMP messages offer senders added control over their messages. Each AMP message can have two key parameters specified by the sender, including reply\_on and gas\_limit. The reply\_on field defines when the message should deliver a callback to the contract, while the gas\_limit feature allows the sender to set an upper limit for the amount of gas to be used.

### Inter Blockchain Communication

The AMP layer also handles IBC calls between ADOs.

The Kernel ADO of each chain is responsible for handling and relaying IBC messages. When the Kernel receives an AMP packet So for example, if ADO 1 (On the Andromeda chain) wishes to send a message to ADO 5 (On another Cosmos chain) :



<figure><img src="../../.gitbook/assets/Multi-Kernel Diagram.jpg" alt=""><figcaption></figcaption></figure>

1. ADO 1 sends an AMP packet to the kernel on the Andromeda Chain.
2. The Kernel receives the packet, verifies it, and forwards it to the kernel on the receiving chain through a channel via IBC-hooks.
3. The Kernel on the second chain receives the packet,verifies it, and then forwards it to ADO 5.&#x20;

It is important to note that every time the Kernel ADO receives a packet, it makes sure to verify that  it is coming from a trusted and wanted source ensuring security and preventing malicious messages to be passed through the system.&#x20;

## AMP Structures&#x20;

ADOs use an Andromeda packet structure to communicate with the AMP layer. This `AMPPkt` structure stores details about the message sender and origin, as well as a vector of `AMPMsg` struct containing the  execute messages that require relaying to the intended ADO recipient.&#x20;

### AMPPkt&#x20;

{% hint style="warning" %}
The packet may contain several messages which allows for message batching. The messags are processed sequentially.
{% endhint %}

```rust
pub struct AMPPkt {
    pub messages: Vec<AMPMsg>,
    pub ctx: AMPCtx,
}

```

| Name       | Type                     | Description                                            |
| ---------- | ------------------------ | ------------------------------------------------------ |
| `messages` | Vec<[AMPMsg](./#ampmsg)> | Any AMP messages associated with the packet.           |
| `ctx`      | [AMPCtx](./#ampctx)      | Additional context to be sent along with the messages. |

### AMPMsg

This struct defines how the kernel parses and relays messages between ADOs. It contains a simple recipient string which may use our namespacing implementation or a simple contract address.&#x20;

{% hint style="warning" %}
If the desired recipient is via IBC, then namespacing (VFS) must be employed.

The attached message must be a binary encoded execute message for the receiving ADO.

&#x20;Funds can be added for an individual message and will be attached accordingly.
{% endhint %}

```rust
pub struct AMPMsg {
    pub recipient: AndrAddr,
    pub message: Binary,
    pub funds: Vec<Coin>,
    pub config: AMPMsgConfig,
}
```

| Name        | Type                                    | Description                                                                  |
| ----------- | --------------------------------------- | ---------------------------------------------------------------------------- |
| `recipient` | [AndrAddr](../common-types.md#andraddr) | The message recipient. Can be a contract/wallet address or a namespaced URI. |
| `message`   | Binary                                  | The message to be sent to the recipient.                                     |
| `funds`     | Vec<[Coin](../common-types.md#coin)>    | Any funds attached to the message, defaults to an empty vector.              |
| `config`    | [AMPMsgConfig](./#ampmsgconfig)         | Some additional configurations for the message to be sent.                   |

#### AMPMsgConfig

The configuration of the message to be sent.

{% hint style="warning" %}
Used when a sub message is generated for the given AMP Msg.

Only used in the case of Wasm Messages
{% endhint %}

```rust
pub struct AMPMsgConfig {
    pub reply_on: ReplyOn,
    pub exit_at_error: bool,
    pub gas_limit: Option<u64>,
    pub direct: bool,
    pub ibc_config: Option<IBCConfig>,
}
```

| Name            | Type                              | Description                                                                              |
| --------------- | --------------------------------- | ---------------------------------------------------------------------------------------- |
| `reply_on`      | [ReplyOn](./#replyon)             | When the message should reply, defaults to Always.                                       |
| `exit_at_error` | bool                              | Determines whether the operation should terminate or proceed upon a failed message.      |
| `gas_limit`     | Option\<u64>                      | An optional imposed gas limit (maximum) for the message.                                 |
| `direct`        | bool                              | Whether to send the message directly to the given recipient or go through the kernel.    |
| `ibc_config`    | Option<[IBCConfig](./#ibcconfig)> | Contains a recovery address which will receive back funds in case an IBC message failed. |

#### IBCConfig&#x20;

Specifies an address that will receive funds back in case an IBC message is sent and failed.

{% hint style="warning" %}
In case this address is not set, the funds will be returned to the original sender of the message.

To recover funds, the user must call the [Recover](kernel.md#recover) execute message found in the Kernel ADO.
{% endhint %}

```rust
pub struct IBCConfig {
    pub recovery_addr: Option<AndrAddr>,
}
```

| Name            | Type                                            | Description                                                  |
| --------------- | ----------------------------------------------- | ------------------------------------------------------------ |
| `recovery_addr` | Option<[AndrAddr](../common-types.md#andraddr)> | The address to receive funds in case the IBC message failed. |

#### ReplyOn

```rust
pub enum ReplyOn {
    Always,
    Error,
    Success,
    Never,
}
```

| Type      | Description                                                             |
| --------- | ----------------------------------------------------------------------- |
| `Always`  | Always perform a callback after a SubMsg is processed.                  |
| `Error`   | Only callback if SubMsg returned an error. No callback on success case. |
| `Success` | Only callback if SubMsg was successful. No callback on error case.      |
| `Never`   | Never perform a callback.                                               |

#### AMPCtx

{% hint style="warning" %}
The `origin` and `origin_username` are not public to prevent disguised attacks.
{% endhint %}

```rust
pub struct AMPCtx {
    origin: String,
    origin_username: Option<AndrAddr>,
    pub previous_sender: String,
    pub id: u64,
}
```

| Name              | Type               | Description                                      |
| ----------------- | ------------------ | ------------------------------------------------ |
| `origin`          | String             | The original sender of the packet. Is immutable. |
| `origin_username` | Option\<AndrAddr>  | The username or VFS name of the original sender. |
| `previous sender` | String             | The previous sender of the packet.               |
| `id`              | u64                | The id of the packet provided  by the kernel.    |

1. When the `AMPPkt` is received by the Kernel, it first checks that the `sender` of the message is an Andromeda ADO.
2. Then it checks the `id` of the packet making sure that it was provided by the kernel.
3. It then sends the `AMPPkt` to the first ADO recipient specified in the first `AMPMsg` from the `Vec<AMPMsg>`.&#x20;
4. When the ADO receives this packet, it is unpacked and executed. Once executed, this `AMPMsg` is removed from the `Vec<AMPMsg>` and the rest of the `AMPPkt` is sent back to the Kernel.
5. The Kernel then recieves the `AMPPkt` and repeats the same operation, relaying it to the next recipient in the next `AMPMsg` until all the messages have been executed.&#x20;

### AMPReceive

All our ADOs have a common execute message to handle receiving an AMP packet from the kernel. When a packet is received by an ADO, it verifies it and proceeds to execute the attached messages.
