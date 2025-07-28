# Glossary

## ADO (Andromeda Digital Object)

An ADO is a modular smart contract designed to perform a specific function within the Andromeda ecosystem. Each ADO is purpose-built. For example, one might manage token vesting, another might run auctions, and another could handle token exchanges. ADOs are the core building blocks of all applications on Andromeda, and they are designed to be composable, meaning they can be connected and work together to power complex decentralized applications.

## App

An Andromeda App is a complete decentralized application composed of one or more ADOs. It acts as a container ADO whose purpose is to bundle, organize, and manage the ADOs within it. This makes it easier to deploy and manage complex applications as a single, unified entity.It represents your full Web3 project, whether it’s an NFT marketplace, a DAO, a payment system, or something entirely custom.

## Module

A Module is a special type of ADO that is not designed to operate independently, but instead extends or modifies the behavior of other ADOs within an app. Modules are added as components inside an Andromeda App to provide additional functionality or logic to one or more connected ADOs.

For example:

* The Rates Module can apply taxes or royalties to token-based sales.
* The Address List Module allows developers to manage permissions by maintaining whitelists or blacklists for specific ADO interactions.

## Primitive

A Primitive is a basic type of ADO designed primarily for data storage. Unlike more complex ADOs that contain logic for interactions or transactions, Primitives serve as lightweight, low-level components used to store and manage on-chain data, such as addresses, strings, numbers, or mappings

## Execute Message

Execute messages are the calls that you can make on an ADO instructing it to perform a specific action. You can read more about executing messages [here](../learning-the-basics/using-execute-messages.md).

## Query Message

Query messges are calls that you can to get specific data from an ADO. They are read-only and do not change the state of the ADO. You can read more about executing messages [here](../learning-the-basics/using-queries.md).

## Modifier

A Modifier is a panel component in the App Builder that represents an execute message for a specific ADO. It allows you to define actions such as minting a token, starting an auction, or transferring ownership. Any modifiers set up will execute directly when an App is published.

Modifiers are useful for automating key steps in your app’s setup or logic flow. Instead of having to trigger these actions manually after deployment, you can build them directly into your app’s launch process.

## Flex File

A Flex File (.flex) is a downloadable file that represents the full configuration of an app you’ve built in the Andromeda Web App. It allows you to save your build locally whether to back it up, pause development, or share it with someone else.

At any time, you can re-import a Flex File back into the Web App to pick up where you left off or to clone a build for reuse. It’s a simple way to store, transfer, and collaborate on app designs across teams or devices.

You can read more about flex files [here](../guides-and-examples/importing-exporting-data.md).

## Embeddable

An Embeddable is a ready-to-use frontend interface for your Andromeda application. Once your app is built and published, you can attach an Embeddable to it, which generates a live, shareable website that users can immediately interact with.

Embeddables make it easy to launch and share fully functional dApps right out of the box. Whether it’s a token sale, NFT drop, or crowdfund, you can give users instant access to your app via a clean, pre-built web interface.&#x20;

You can read more about embeddables [here](../guides-and-examples/embeddables/).
