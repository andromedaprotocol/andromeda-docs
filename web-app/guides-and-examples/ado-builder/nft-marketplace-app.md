# NFT Marketplace App

In this guide, we will be building a simple NFT Marketplace app from scratch.&#x20;

### Defining our App

Before we begin building the app in the ADO Builder, let's define its features and functionality. For this build, we will assume a team is looking to create a simple marketplace for an NFT collection where each NFT is sold for a fixed price.&#x20;

### Defining our Components

To create the App we described above, we need the following components:

* [CW721](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to mint the NFT collection
* [Marketplace](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to sell the NFTs

## Building the App

Open a new empty project in the ADO Builder and give the app an appropriate name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-18 at 6.38.28 PM.png" alt=""><figcaption></figcaption></figure>

Add a CW721 component and fill in the fields:&#x20;

{% hint style="info" %}
Use your connected wallet for the **Minter Address** field.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-18 at 6.39.11 PM.png" alt=""><figcaption></figcaption></figure>

Next, add the marketplace component to the build. Other than the optional modules, the Marketplace ADO has two fields:

* **Authorized Sale Type:** Allows us to define a CW20 ADO  whose tokens can be set as the purchasing token for the NFT sales. This would restrict the CW20 ADOs allowed to be set to the one defined here. In case we just select native, then we can set any CW20 as the purchasing token. Using a native token is always accepted. This will be defined later on when we configure auction parameters. So for now we select the **Native** option.
* **Modules:** Allows users to add modules to the auction. For now, you do not need any modules, so you can keep it empty.

{% hint style="info" %}
Modules are currently disabled, but will be available in our next release.
{% endhint %}

The app is now ready to be published to chain. Go ahead and publish:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-18 at 6.42.50 PM.png" alt=""><figcaption></figcaption></figure>

## Setting up the NFT Sales

The app has been published to chain and is ready to be interacted with. The steps we will perform in this section are the following:

1. Mint the NFT collection using our CW721
2. Start sales on each NFT minted in the collection by sending it to the marketplace

### Minting the NFT Collection

The easiest way to mint the NFTs is by using our **Batch Mint** message that can mint several NFTs at a time. Choose the **Batch Mint** message from the list of execute messages for a CW721 and mint some NFTs:

{% hint style="warning" %}
Make sure that the owner of the NFTs is your address.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-18 at 6.48.00 PM.png" alt=""><figcaption></figcaption></figure>

Once done with the first token, add another token and repeat the process until you have 3 NFTs and then publish.

### &#x20;Starting Sales on Each NFT

In order to start a sale on an NFT, you need to send that NFT to the Marketplace component with the sale parameters attached. This is very similar to what we did to start an auction on an NFT. &#x20;

Select **Send NFT** from the list of executes of the **CW721** component. The “**Recipient Address**” is the Marketplace component address which can be fetched from the assets page. For the “Token/NFT Name” field, put the token Id of the NFT you want to start a sale on:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-18 at 6.50.54 PM.png" alt=""><figcaption></figcaption></figure>

Next you have the “**Custom Message**” field. Since you are sending to a marketplace component, select **Marketplace** and then select “**CW721 start sale**” under “**Select Message**”.&#x20;

Here you will have to specify the following:

* If we want to use a CW20 token as the purchasing token. I will not check the box as I intend to use native.
* Specify the coin denom accepted for purchasing the NFT. Since I am building on the Stargaze chain, and I want to use a native tokens as the bidding token, I will specify **ustars**.
* The starting time of the sale. This is specified through an [epoch time](https://www.google.com/url?q=https://www.epochconverter.com\&sa=D\&source=editors\&ust=1684253771700484\&usg=AOvVaw1ibK4NOXubSN0uNcYHSI6F) in milliseconds. Selecting none means the sale will start immediately after the message is published. I will use that option.
* The duration of the sale in milliseconds. I want the auction to run for 2 hours. .
* The price of the NFT. I will choose 100000.&#x20;
* Recipient which is the address to receive the funds from a succesfull NFT sale. I will select none as it will default to my address (The sender of the NFT)

{% hint style="info" %}
Not including a start time means the sale is started immediately.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-18 at 7.35.33 PM.png" alt=""><figcaption></figcaption></figure>

Once you publish, the first NFT is sent to the marketplace ADO where it is listed for sale for the specified price. Users are able to buy the NFTs from the marketplace which will send the funds to your address and send them the NFT.

We have succesfully set up one NFT sale. You can do the same for each minted NFT. You can also use the **Multi Execute** option, to set up multiple messages at a time. This would allow you to send multiple NFTs to the marketplace at a time.

{% hint style="info" %}
If you want to try buying some of the NFTs from the sale and then checking the results, check our "[Using CLI to Interact with Apps](../using-cli-to-interact-with-apps.md)" section.

If you are interested in using queries on your App components, check out our [Using Queries ](../../learning-the-basics/using-queries.md)section to learn about that process.
{% endhint %}
