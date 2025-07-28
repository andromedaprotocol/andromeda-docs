# CW721: Non-Fungible Token (NFT) Standard

## Introduction

The **CW721** standard is the standard smart contract used to create NFTs. It was inspired by the **ERC-20** standard on the Ethereum network. Whenever we need to build a dApp that uses NFTs in some way, we will need to work with the **CW721** ADO. The Andromeda CW721 ADO, implements the CW721 standard with some custom additions to it. For example in our CW721 ADO, you can directly sell and NFT through it without the need of a third party ADO. You can read the full documentation on the CW721 ADO [here](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw721).

### Key Features of the CW721

* **Unique Identifiers:** Each NFT minted by the CW721 is assigned a **unique** Id known as the token id. This ensures that each NFT is distinct from another within the same ADO. Whenever you need to reference a specific NFT, the token id will be used.
* **Custom Metadata:** Custom metadata can be attached to CW721 tokens, allowing for rich descriptions, properties, and functionalities. This data is usually stored off-chain on some storage site like IPFS. You can check out the [**Setting Up NFT Metadata**](../../guides-and-examples/ado-builder/building-your-first-nft-collection/setting-up-nft-metadata.md) section to learn more about hosting NFT metadata for your collection.

{% hint style="info" %}
Each created NFT in the CW721 has an owner address which is assigned upon creation.
{% endhint %}

* **Transferability:** CW721 tokens can be transferred between addresses, enabling ownership transfer of digital assets.&#x20;

### Example Use Cases

* **Digital Art:** CW721 tokens can represent unique pieces of digital art, providing artists with a platform to tokenize and sell their creations securely.
* **Gaming Assets:** In the gaming industry, CW721 tokens can represent in-game items, characters, or collectibles, allowing players to trade or sell their digital assets securely.
* **Real-World Assets:** CW721 tokens can represent ownership of real-world assets such as real estate, artwork, or any physical product like shoes, tickets or anything else.

### Andromeda Compatible ADOs&#x20;

In Andromeda, we have created several ADOs that work with the CW721 ADO alllowing users to create any use case they desire. These ADOs are the following, with many more to come:

* **Marketplace:** An ADO that interacts with the CW721 to create a custom marketplace for your NFT collection.
* **Auction:** An ADO that intercats with the CW721 to create custom english auctions for your NFT.
* **Crowdfund:** An ADO that intercats with the CW721 to create a crowdfund for your project throught the sale of NFTs.
