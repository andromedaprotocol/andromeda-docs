---
description: >-
  At the heart of the aOS, ADOs provide the flexible and interoperable building
  blocks for developers and creators to rapidly interact with functionality
  on-chain.
---

# Andromeda Digital Objects

In many ways, you can think of an ADO as a file. It has an owner, properties, functionality, and possibly value. Whether its a financial position, such as a loan or a position in a liquidity pool, it can be reflected as an ADO in the system. Or an NFT. Or a time locked staking position with a validator. Or a splitter wallet.

What's revolutionary about this system is that these ADOs can natively talk to each other without the user needing to provide any low-level programming.

Rust developers can extend functionality in the system by packaging their code into our ADO Package System (ADOP) and submitting their work to the DAO for approval. Once submitted, and approved, the functionality is now available for everyone to use. Think of this as `apt` or `dpkg` for blockchains.

### Architectural Philosophy

Andromeda borrows from the Unix/Linux way of developing user tools, each command should do one thing and do it really well. Each ADO, by itself, has limited functionality, but when combined with other ADOs, the real power of the system can be realized.

The secret sauce in this architecture is the [aOS Messaging Protocol](../aos-messaging-protocol.md). This allows for all objects in the system to easily communicate with any other object without the need for one off adaptors - similar to how [standard streams](https://en.wikipedia.org/wiki/Standard\_streams) work in traditional operating systems.
