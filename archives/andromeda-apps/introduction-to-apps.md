# Introduction to Apps

### Andromeda Apps

Andromeda is a central hub for building fully decentralized web-3 Apps in the Cosmos ecosystem. To do so, we have created the App ADO which groups all the different ADOs needed for a dApp under one ADO. This facilitates the organization of the project and the communication between the different parts.&#x20;

### Features

1. Fully decentralized dApps
2. Instantiate all of the ADOs with one go
3. Use our custom name-spacing system to integrate and connect ADOs
4. Interact with the entire dApp and ADOs from one source
5. Easily customize permissioning and ownership of all ADOs

### Andromeda CLI

{% hint style="info" %}
All of the Apps  we will go through can be built using our web-application which is user-friendly interface that makes building application a seamless process. If interested you can check the guides of using the web-app [here](https://docs.andromedaprotocol.io/guides/guides-and-examples/ado-builder/building-your-first-app).
{% endhint %}

We will be building the Apps using the [Andromeda CLI](broken-reference), so make sure you have it dowloaded. If not you can grab the latest version by running:

```
sudo npm i -g @andromedaprotocol/cli
```

We will be building all our examples using our [wasm commands](../andromeda-cli/wasm.md):

* wasm instantiate
* wasm execute
* wasm query

All of the builds can be performed using our [ado commands](../andromeda-cli/ado.md) which would prompt you to fill in the messages field by field. Feel free to experiment and build any of the use cases the way you find easiest.&#x20;

### Converting from JSON to Base64

In the examples we will cover, there will be many instances where we have to convert JSON messages to Base64. To convert your JSON message to a base64 encoded message, you will need to use an online converter. One thing to keep in mind is that the JSON message needs to be one line when converting. For example the conversion below is **Invalid** as the message is spread accross 7 lines:

<figure><img src="../.gitbook/assets/Screen Shot 2023-06-15 at 6.36.00 PM.png" alt=""><figcaption></figcaption></figure>

The correct conversion looks like this:

<figure><img src="../.gitbook/assets/Screen Shot 2023-06-15 at 6.38.13 PM.png" alt=""><figcaption></figcaption></figure>

### Getting Component Addresses

In the examples we will cover, you will need to interact with the components of the app after you instantiate it. You can run the following in CLI to get the contract address of each component:

```
wasm query <app-contract-address> '{"get_addresses_with_names":{}}'
```

This will return the name of each component, along with the contract address.

### What's Next ?

In the next sections, we will be going through some examples on building and deploying an Andromda App from scratch using the Andromeda CLI. All the Apps will be built on the Andromeda testnet and will be local (Not cross-chain). We will go through a variety of popular dApps that can be built using Andromeda. Keep in mind that these are only a very few use cases out of the thousands that can be built.&#x20;
