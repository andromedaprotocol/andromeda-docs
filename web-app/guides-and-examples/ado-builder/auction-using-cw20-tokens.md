# Auction Using CW20 Tokens

In an [earlier example](creating-your-first-nft-auction-house.md), we created an Auction application that takes CW721 tokens (NFTs) and creates custom auctions for each NFT.  For bidding, we had used native tokens. In this guide, we will be using CW20 tokens as the bidding token for the NFTs instead. **Lets get started!**

{% hint style="warning" %}
If you havent built any Apps yet, it is recommended you go through the first 3 to learn the basics.
{% endhint %}

### Defining our App

Before we begin building the app in the ADO Builder, let's define its features and functionality. For this build, we will assume:

1. &#x20;A team is looking to create a private auction on their new NFT collection.
2. The team only wants holders of their CW20 token to be able to bid in the auction.

### Defining our Components

To create the App we described above, we need the following components:

* [CW721](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to mint the NFT collection.
* [Auction](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/auction) component to host he NFT auctions
* [CW20](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/cw20) component to create the CW20 token used in bidding.

## Building the App

Open a new empty project in the ADO Builder and give the app an appropriate name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 8.41.53 PM (1).png" alt=""><figcaption></figcaption></figure>

#### CW721 Setup

Let us set up our NFTs. Add a **CW721** component and fill in the fields:

{% hint style="info" %}
Make sure to specify the minter address as your connected address to be able to mint NFTs without having to change your wallet.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 8.46.01 PM.png" alt=""><figcaption></figcaption></figure>

#### CW20 Setup

We also need to create a CW20 token. So add a CW20 component and customize it as you see fit:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 8.59.26 PM.png" alt=""><figcaption></figcaption></figure>

For Starting balances, I will giving 3 of my addresses 1000 tokens to use for bidding:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 9.03.13 PM.png" alt=""><figcaption></figcaption></figure>

#### Auction Setup

Our last component will be the **Auction** component. Add one to the build and let us take a look:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 8.49.03 PM.png" alt=""><figcaption></figcaption></figure>

Here we need to define the following:

**-Authorized CW721 Addresses:** The CW721 ADOs allowed to create auctions in the Auction component. We will specify the **CW721** component by referencing its assigned name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 9.07.27 PM.png" alt=""><figcaption></figcaption></figure>

\-**Authorized Sales Type:** Whether to allow CW20 tokens to be used as bidding tokens. Since we want to use CW20s as the bidding token, select CW20. We then need to specify the CW20 ADO that can be set as the bidding token. We want to authorize our CW20 component, so we reference it by its assigned name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 9.13.14 PM.png" alt=""><figcaption></figcaption></figure>

Now that all the component have been set up, we can go ahead and publish to chain:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-29 at 9.17.17 PM.png" alt=""><figcaption></figcaption></figure>

### Minting the NFTs

Before we can start any auctions, we need to create the NFTs to be auctioned. Select the **Mint** message from the **CW721** component and fill in the information for your NFT, then publish to chain:

{% hint style="info" %}
If you are intereseted in setting up the NFT metadata (Token Identifier), you can check out the "[**Setting Up NFT Metadata**](building-your-first-nft-collection/setting-up-nft-metadata.md)" section.

Feel free to use the Batch Mint execute to mint several NFTs in one go.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 6.01.34 PM.png" alt=""><figcaption></figcaption></figure>

To make sure the NFT minted successfuly, let us query the CW721 component for the number of minted NFTs it has. Select the **Num Tokens** query for the **CW721** and then press **Query** to get the number of NFTs this CW721 has created. I minted one NFT, so the result should return 1:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 6.09.40 PM.png" alt=""><figcaption></figcaption></figure>

### Customizing the NFT Auction

Now that we have our NFT minted, we need to send it to the **Auction** component and specify the auction options. To do so, we will use the **Send NFT** execute message from the CW721 component:

{% hint style="warning" %}
If you have more than 1 NFT, you could use the [multi-execute](../../learning-the-basics/using-execute-messages.md#multi-execute) to send several NFTs to the auction component in one go.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 6.37.51 PM.png" alt=""><figcaption></figcaption></figure>

**Here we specify:**

{% hint style="info" %}
The Recipient address can be referenced by the contract address (found in assets page) or by referencing its assigned name.
{% endhint %}

* **Recipient Address:** The address to receive the NFT we are sending. In our case, we want the auction component to receive the NFT so we specify that.
* **Token Id:** The Id of the token we are sending. In my case I assigned the NFT an Id of "1" (As seen in the minting section).

Next, we need to add a custom message that contains the auction parameters. Select **Custom Message** and **Auction** from the dropdown options. Then for "**Select Message**", choose "**cw721-start-auction**":

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 6.50.35 PM.png" alt=""><figcaption></figcaption></figure>

**For the Auction options, we specify the following:**

* **Uses CW20:** Whether the auction will use a CW20 token for bids. Since that is our goal, check the box.

{% hint style="warning" %}
When using native tokens, you specify the denomination like uandr, ustars etc... When using CW20 tokens, you specify the contract address of the CW20 ADO.
{% endhint %}

* **Coin Denomination:** The contract address of the CW20 component.  We can get it from the assets page.
* **Auction Start Time:** Specifiest the date and time the auction will start meaning users can start bidding. Selecting the **None** option means the auction will start as soon as the NFT is sent to auction.
* **End Time:** The date and time that the auction concludes, meaning noone can place any bids anymore.
* **Minimum Bid:** The minimum amount of tokens that can be used to bid. I will use 250 for this example.
* **Recipient:** The receiver of the funds for a successful auction on the NFT. Selection the **None** option means the funds will go to the address that started the auction.
* **Whitelist**: A list of addresses that are allowed to bid on the NFT. Selecting none means that anyone is able to bid.

Now that all the auction options have been set up, let us publish the message.&#x20;

### Bidding on the NFT

Now that the NFT auction has been set up and published to the chain, we can start bidding on the NFT. For this, I will use the 3 addresses that I had used as recipients for my cw20 tokens.&#x20;

{% hint style="info" %}
Bidding on the NFT is usually done through the front-end interface of your application like our [embeddables](../embeddables/). For this example I will not show the steps of bidding.
{% endhint %}

#### Place a bid of 500

{% embed url="https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/07F66789C5BFD35FBABF6833A34B176DA190A7198901D8C3B4413DBC8D02B1C6" %}

**Checking the Auction's bids using the Auction State Query:**

Using the **Auction State** query from the list of queries for the auction component, we can check the current auction information for our NFT auction:

{% hint style="warning" %}
The auction Id is 1 here since this is the first auction that was held by the auction component. If we send another NFT to auction, then it would have an Auction Id of 2 and so on.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 7.49.06 PM.png" alt=""><figcaption></figcaption></figure>

As we can see the "**high\_bidder\_amount**" is currently 500. The address of the highest bidder is dispayed above it.

**Placing a bid of 750**

A new user places a bid of 750 tokens:

{% embed url="https://explorer.testnet.andromedaprotocol.io/galileo-4/tx/07EC983322E638F2CC339E0532684AEDEC328E83D4D42F6E9A9D750857903E2F" %}

**Auction State:**

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 7.52.17 PM.png" alt=""><figcaption></figcaption></figure>

The 750 is now the highest bid, and thus if the auction concludes, the user that placed the bid will receive the NFT. The bidding can continue until the end time of the auction is reached.&#x20;

### Checking the Results Before the Auction Concludes

Before the auction ends, let us check a few things. First, the CW20 balance of the address that put the NFT up for auction. We can do this by using the **Balance** query for the CW20 component:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 7.59.17 PM.png" alt=""><figcaption></figcaption></figure>

Next check the owner of the NFT we have up for sale:

{% hint style="info" %}
The current owner of the NFT is the Auction component.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-01 at 8.09.17 PM.png" alt=""><figcaption></figcaption></figure>

### Checking the Results After the Auction Concludes

After the auction ends (End time is reached), let us check the balance and NFT owner again. Before we can do that, we need to perform the **Claim** execute message to claim the NFT and send the funds to the seller:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-02 at 6.50.39 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-02 at 6.52.51 PM.png" alt=""><figcaption></figcaption></figure>

**Balance of the NFT Seller**

After executing the **Claim** message, the CW20 tokens should be in the seller's balance. Let us check by performing the **Balance** query in our CW20 component:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-02 at 6.57.49 PM.png" alt=""><figcaption></figcaption></figure>

As we can see, the 750 tokens are now with the NFT seller.

**Owner of the Sold NFT**

Let us check who owns the NFT:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-05-02 at 7.01.19 PM.png" alt=""><figcaption></figcaption></figure>

As we can see, the owner is the same address that bid the 750 CW20 tokens.
