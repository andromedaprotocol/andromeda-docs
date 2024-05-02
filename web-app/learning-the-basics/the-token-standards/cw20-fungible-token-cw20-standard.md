# CW20: Fungible Token (CW20) Standard

## Introduction

The **CW20** standard is the standard smart contract used to create coins. It was inspired by the **ERC-20** standard on the Ethereum network. Whenever we need to build a dApp that needs a new coin, we will need to work with the **CW20** ADO. The Andromeda CW20 ADO, implements the CW20 standard with some custom additions to it. You can read the full documentation on the CW721 ADO [here](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw721).

### Key Features of the CW2O

* **Divisibility:** CW20 tokens are divisible, meaning that they can be divided into smaller units, enabling microtransactions and flexible asset management. This is done through the configuration of the decimal field in a CW20. You can read more about that in the ADO docs.
* **Supply Control:** Developers can specify the total supply of CW20 tokens and manage token minting, burning, and distribution according to predefined rules.
* **Efficient Transactions:** CW20 tokens enable fast and low-cost transactions within the Cosmos network, making them suitable for various use cases, including payments and tokenized assets.

### Difference between CW20 and a Native Token

&#x20;When it comes to building the Andromeda Apps, there will be several instances where you can pick between a CW20 token and a native token. This is mostly the case when it comes to choosing the token the user will use as funds in the application.&#x20;

**CW20 tokens** are smart contract based tokens, meaning they are created by smart contracts, mainly our CW20 ADO. This token can be created by anyone and can be customized by the user however they want.

**Native tokens** on the other hand are the tokens embedded into the chain you are using. They are used to run the infrastructure of the chain like paying fees, network security, and governance (Proposal voting). As a user, you cannot create your own native token on a blockchain. Usually most chain operate with 1 native token although there are instances of chains having several.

### Example Use Cases

* **Stablecoins:** CW20 tokens can be used to create stablecoins pegged to fiat currencies or other assets, providing a stable medium of exchange and store of value within the Cosmos ecosystem.
* **Governance Tokens:** Projects can issue CW20 tokens as governance tokens to enable community participation in decision-making processes and protocol upgrades.
* **Meme-Coins:** With Andromeda, you can create your owne meme-token in minutes using the CW20.
* **Utility Token:** Can be utilized for various purposes such as accessing platform features, facilitating transactions, incentivizing user participation.

### Andromeda Compatible ADOs&#x20;

In Andromeda, we have created several ADOs that work with the CW20 ADO alllowing users to create any use case they desire. These ADOs are the following, with many more to come:

* **CW20-Staking:** Allows you to set up staking for your CW20 token.
* **CW20-Exchange:** Allows you to exchange CW20 tokens for other tokens ( CW20 or Native).
* **Merkle-Airdrop**: Allows you to perform a [merkle-airdrop](https://medium.com/smartz-blog/merkle-airdrop-the-basics-9a0857fcc930) for your CW20 tokens.

{% hint style="warning" %}
CW20 can also be used as the purchasing token for NFT sales in auctions and marketplaces.
{% endhint %}
