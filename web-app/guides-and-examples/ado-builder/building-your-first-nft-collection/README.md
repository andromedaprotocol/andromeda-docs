# Building your First NFT Collection

{% hint style="warning" %}
If unfamiliar with building Andromda Apps on the web-app, refer to "[Building Your First App](../building-your-first-app.md)" section to learn the basics before proceeding with this guide.
{% endhint %}

In the last guide, you learned the basics of building an Andromeda App from scratch. We built the most basic application which consists of one component and stopped before executing any messages as the app served no purpose. In this guide, you will be creating your first NFT collection using Andromeda Apps. Lets jump right into it!

Open up a new empty project and give the app a suitable name:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.35.29 PM.png" alt=""><figcaption></figcaption></figure>

Select “**Add App Component**” to add an ADO to the app. Since we want to build an NFT collection, you need our CW721 ADO:

{% hint style="warning" %}
You can use the search bar to locate ADOs by name.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.36.51 PM.png" alt=""><figcaption></figcaption></figure>

If you are not familiar with this ADO, you can click on the documentation link below the description which will open up the ADO's own documentation page.

This page contains detailed information about the ADO, its function, and each of its messages. It is a great resource for understanding how the ADO works and how to use it effectively within your apps:



<figure><img src="../../../.gitbook/assets/Screen Shot 2024-01-12 at 4.27.09 PM.png" alt=""><figcaption></figcaption></figure>

Let's get back to the build. Add the CW721 to the app:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-01-12 at 4.30.41 PM.png" alt=""><figcaption></figcaption></figure>

The first thing to do when adding any component is changing its name.  Although it does not matter when we change the name, it is best practice to do so in the beginning as there will be many cases where you will forget to do so later on. Our apps are designed so that components are able to reference each other by these assigned names. To change the name of a component, press the pencil icon to the right of the ADO type:

{% hint style="warning" %}
You can keep the default names assigned to the components. The name assigned by default is the ado type. For example if you add 2 CW721 components to an app, then they will be named "cw721-1" and "cw721-2".  Just make sure to reference the correct component name when needed.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.39.20 PM.png" alt=""><figcaption></figcaption></figure>

Choose a suitable name for the component and lets start configuring our CW721 panel. Every component has a set of fields that can be filled in. There are two types of fields:

1. **Required** : Required fields will have a red asterisk symbol next to the field name. This signifies that this field has to be filled in order to deploy the component.
2. **Optional**: Optional fields will not have an asterisk symbol. It is up to the users if they want to set these fields. They usually offer additional customization to the component.

The CW721 panel has 3 required fields:

1. **Minter Address** : The address allowed to mint NFTs from the component.
2. **Token/NFT Name**: The name of the NFT collection.
3. **Token/NFT Symbol**: The symbol of the NFT collection.

For the minter, I recommend using your connected address to avoid having to switch wallets when you get to minting. Choose any suitable name and symbol for the collection:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.43.07 PM.png" alt=""><figcaption></figcaption></figure>

The App is ready to be published to the chain, but before you do so, let’s export the build so it can be reimported later on or shared with other people. To export your app, click on the download symbol to the left of the publish button:

<figure><img src="../../../.gitbook/assets/Screen Shot 2023-05-16 at 7.29.14 PM.png" alt=""><figcaption></figcaption></figure>

When pressed, select a name for your build and proceed by selecting save. You will notice a .flex file is automatically downloaded. This file can then be imported to the ADO Builder which will automatically load up what you have built.

{% hint style="info" %}
There are multiple ways to export apps and data. More about that can be found in the "[Importing/Exporting Data](../../importing-exporting-data.md)" section.
{% endhint %}

Now that you have saved a copy of the App, let's go ahead and publish:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.45.39 PM.png" alt=""><figcaption></figcaption></figure>

You are redirected to the assets page when you publish.

{% hint style="warning" %}
It might take up to 30 seconds for the app to appear in the assets page.
{% endhint %}

First let us check the components. To do so, press on the expandable to the right of the three dots:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.46.47 PM.png" alt=""><figcaption></figcaption></figure>

This will expand the app to show each of the components inside the app. The aim in this example is to mint an NFT collection. To do so, you will have to use execute messages on the CW721 component.

Before you do that, let us have a small discussion on permissions and Apps. There are three main types of execute messages when it comes to permissioning:

* **ADO Owner Restricted Messages**: Execute messages that can only be executed by the ADO owner. For example, only the ADO owner can transfer ownership of an ADO to another address.
* **Asset Owner Restricted Messages**: Execute messages that can only be executed by the owner of a certain asset. For example, transfering an NFT or CW20 token to another address can only be done by the owner of that NFT/CW20 token. Even the contract owner is not allowed to to perform these messages.
* **Non-Restricted messages**: Execute messages that can be executed by any address.

{% hint style="warning" %}
These are the three **main** types of permissions used in most ADOs. There are cases where specific addresses are permissioned to perform a specific task. One example would be the minter field in a CW721 which specifies the only address allowed to call the mint execute message on a CW721.

We also offer custom permissioning that can be set up by the owner to bypass these restricted messges. We will see examples of this in later examples.
{% endhint %}

By default, when we create an app, the address of the user instantiating the App is assigned as the owner of all the other components. This means that all **ADO Owner Restricted Messages** of the components can only be sent by this user.

In case a user wants the App to be the owner of the components, they can transfer ownership of each component to the App address. The App component has two ways to allow us to perform these messages. Let us take a look at the execute messages of the App component:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 5.54.33 PM.png" alt=""><figcaption></figcaption></figure>

For now, we are interested in learning about **Claim Ownership** and **Proxy Message**.

Proxy Message is a **Contract Owner Restricted Message** (Only the App owner can call it) which will take your message and send it through the App component. When we execute on components, we are given the choice of using a proxy message, or directly executing on the component itself. This choice is represented with a toggle for each execute message:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 6.32.38 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Never use **Proxy** for **Asset Owner Restricted Messages** as you will get an unauthorized error. This is not the case only if the App address is the owner of the asset.

Proxy message is only needed if you have given ownership of the components to the App.
{% endhint %}

Now that you understand the above concepts, let's get back to our app. For a CW721, the **Minter Address** is the **ONLY** address allowed the mint NFTs ie. call the **Mint** execute message. This means that the **Mint** message cannot be proxied since the App address is not authorized to call it.

Select the **Mint** message from the list of execute messages for the **CW721** component. The required messages for minting an NFT are the following:

* **Minted Token ID**: A unique Id for that NFT. No other NFT can have the same token ID. Used in a lot of cases to reference that NFT.
* **Token Owner Address**: The address that will own the minted NFT and thus can sell/transfer/interact with the NFT once minted.
* **Original Publisher**: The publisher of the NFT. Usually is the project name.
* **Token Identifier:** A URL pointing to the NFT metadata which includes key information about each NFT. To learn the process of setting up this data, you can check out the [Setting Up NFT Metadata](setting-up-nft-metadata.md) section. For this example I will keep it empty.

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 7.26.31 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
You can export the panel data to be used for other panels later on similar to how we can export the entire build. More on that can be found in the "[**Importing/Exporting Data**](../../importing-exporting-data.md)" section.&#x20;
{% endhint %}

Once the fields are filled in, proceed to publishing.

Now that you have minted your first NFT, let us mint 2 more. This time you will use the **Batch Mint** execute message. **Batch Mint** is a custom message that we have added to the CW721 standard to be able to mint several NFTs in one go:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 7.31.40 PM.png" alt=""><figcaption></figcaption></figure>

For each NFT you want to mint,  select “Add Item”. For this example, add 2 items and fill the fields with information similar to what we did with the Mint message. When done, publish to the chain.

{% hint style="warning" %}
Don't forget that the Token ID needs to be unique.
{% endhint %}

You have minted three NFTs for the collection. There is no limit to the number of NFTs you are allowed to mint but three will be enough for now.\
\
To conclude this example, let’s sell one of the NFTs using the **Transfer Agreement** message. **Transfer Agreement** is another custom message that we have added to the CW721 standard to allow selling/buying NFTs without the need of a third component. It allows the NFT owner to set a price for the NFT. A buyer is then able to transfer the NFT to their account on the condition that they attach the required funds with the message. Select **Transfer Agreement** from the list of execute messages for the CW721:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 7.37.38 PM.png" alt=""><figcaption></figcaption></figure>

The main fields to specify here are:

* **Amount**: The price of the NFT.
* **Denom**: The denomination of native token used to buy the NFT.
* **Purchaser**: Who is allowed to purchase the NFT.
* **Token** **Id**: The token Id of the NFT to sell.

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-03-14 at 7.40.18 PM.png" alt=""><figcaption></figcaption></figure>

Here I have specified the amount as 1000, and the denom as ustars. This means I want to sell my NFT for 1000 ustars. For the purchaser, you can specify an address, meaning only that address is allowed to buy your NFT, or you can put \*  (Asterisk) meaning that any address is allowed to buy the NFT.

The denom for funds on the Andromeda chain will always be uandr. Similarly, ujuno for Juno, ustars for Stargaze and so on. This is because we are using native tokens meaning the main token for the chain.

{% hint style="warning" %}
Although rare, some chains might have more than one native token. The native token does not necessarily need to conform with the above naming. For example the native token for the Injective chain is inj and not uinj .&#x20;
{% endhint %}

After filling in the fields, publish the message to the chain. Now, any address is able to transfer this NFT to their account by calling the [TransferNFT](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw721#transfernft) message as long as they attach 1000 ustars to the message which will be credited to your address (The seller). This is the only case where an address, other than the NFT owner, is able to transfer an NFT they do not currently own.&#x20;

This concludes the second guide. Feel free to play around with your App (Refer to docs to see the messages for the components and their function).

{% hint style="info" %}
If you want to try buying the NFT you just put up for sale check our "[Using CLI to Interact with Apps](../../using-cli-to-interact-with-apps.md)" section.

If you are interested in using queries on your App components, check out our [Using Queries ](../../../learning-the-basics/using-queries.md)section to learn about that process.
{% endhint %}

The Apps you have built so far are quite simple, but if you're ready to take things to the next level, check out the next guide “[Creating your First NFT Auction House](../creating-your-first-nft-auction-house.md)” where you will create custom auctions for your NFT collection.\
