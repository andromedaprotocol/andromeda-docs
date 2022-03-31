# Recipient

### Definition

When building ADOs, the recipient of a function/message could be another ADO or an address. This is why we use the Recipient enum to differentiate between a user recipient and an ADO recipient.

The `Recipient` enum looks as follows:&#x20;

```rust
pub enum Recipient {
    Addr(String),
    ADO(ADORecipient),
}
```

**Addr:** When the recipient address is not another ADO. It is assumed that it is a valid address.

**ADO:** When the recipient address is another ADO. Uses the `ADORecipient` struct defined below.

#### **ADORecipient**

The address can be either a contract address or the human-readable identifier used in a mission contract.

```rust
pub struct ADORecipient {
    pub address: AndrAddress,
    pub msg: Option<Binary>,
}
```

| Name      | Type            | Description                                 |
| --------- | --------------- | ------------------------------------------- |
| `address` | AndrAddress     | Check [AndrAdress](recipient.md#andradress) |
| `msg`     | Option\<Binary> | An optional message to attach.              |

### AndrAddress

A struct used to reference another ADO contract. Can be either an address or identifier of an ADO in a mission.&#x20;

```rust
pub struct AndrAddress {
    pub identifier: String,
 }
```
