# Extended Marketplace

In a [previous](nft-marketplace-app.md) example, we built a simple NFT marketplace that can be used to sell an NFT collection for  native funds. In this guide, we will be extending that build to include some of the cool features that can be used in the aOS.&#x20;

### Defining our App

{% hint style="info" %}
The App will be built on the Andromeda chain.

The NFTs will be sold for ANDR tokens.
{% endhint %}

For this build, let us assume the following:

* There are three individuals who work in an art collective and they've been selected to work in creating a small project.
* Each individual will be creating an art piece (NFT) under a theme and submitting it to be sold in the marketplace. The proceeds of these NFTs will go to each of the artists respectively.
* There will be one collaborative piece that they would be working together and present as a final NFT. The proceeds from this NFT will be sent to a splitter to be evenly split between the three artists.
* One of the three artists will be administrating the marketplace (minting the NFTs and sending them to the marketplace). The other two will be given a one time permission to mint an NFT.
* A 5% additive tax (added to the price) will be taken from the sold NFTs for administrative costs

### Defining the Components

To create the App we described above, we need the following components:

* [CW721](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to mint the NFT collection and set up the permissions.
* [Marketplace](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to sell the NFTs and add the taxes.&#x20;
* [Splitter](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/splitter) component to automatically split the funds from the collaborative NFT to the three artists.

## Building the App

Open a new empty project in the ADO Builder and give the app an appropriate name:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 3.13.54 PM.png" alt=""><figcaption></figcaption></figure>

**Add a CW721 component and fill in the fields:**&#x20;

{% hint style="info" %}
Use your connected wallet for the **Minter Address** field.

I use the name "Snail Collection" as this will be the theme for the NFTs.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 3.17.41 PM.png" alt=""><figcaption></figcaption></figure>

**Next, we need to add the marketplace component:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-11 at 10.32.13 PM.png" alt=""><figcaption></figcaption></figure>

Here we specify the CW721 and CW20 addresses allowed to interact with our marketplace. For CW721 we specify the NFT component of our App. For CW20, we can keep it empty, as we will be using native funds to sell the NFTs.&#x20;

{% hint style="warning" %}
In case you are looking to sell the NFTs for CW20 tokens, then Authorized CW20 addresses must be set.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-11 at 10.39.09 PM.png" alt=""><figcaption></figcaption></figure>

**Finally we add our splitter component:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 3.36.21 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
We are assuming that one of the artists is us, so specify the connected address as one of the recipients.

We can specify None for the rest of the options.&#x20;
{% endhint %}

Here we specify:

* **Lock Time:** A lock time of 30 days is specified. This ensures that the split used by the splitter (even distribution to the 3 artists) cannot be maliciously changed by the owner until the sale is over.
* **Recipients:** The percentage and address for each recipient. For each percentage, we use 0.33 to specify 33% and we specify the address of the artist. Add 3 recipients in total.

**The app is now ready to be published to chain. Go ahead and publish:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 5.40.43 PM.png" alt=""><figcaption></figcaption></figure>

## Setting Up the App

First thing we will do is set up our rates (Tax) and permissions for our application. As this is the first time we cover these features, let us quickly go over what they entail:

* **Rates:** Allow the owner to set a fee (tax or royalty) on a specific action (execute message) in an ADO. In our case, we will add a 5% tax on buying an NFT from the marketplace.
* **Permissions:** Allow the owner to set restrictions  (blacklist, whitelist or limited uses) for certain addresses on specific ADO actions (execute messages). In our case, we will be permissioning the 2 artists to mint an NFT from the CW721. This action was previously exclusive to the specified minter, but with permissions, we can bypass this.&#x20;

### Setting Rates

{% hint style="warning" %}
Not all actions can be bypassed. Contract Owner resticted messages cannot be permissioned.
{% endhint %}

To set up the 5% tax on the NFT sales, select the Rates message from the list of modifiers for the marketplace component:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 5.50.01 PM.png" alt=""><figcaption></figcaption></figure>

This will open up the following panel:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 5.51.29 PM.png" alt=""><figcaption></figcaption></figure>

**Here we select/specify the following:**

* **Set Rate**: This indicates we are adding a rate to the ADO.

{% hint style="info" %}
The action is case sensitive.

You can also set up the rates in a seperate rates ADO and then select "Contract" in the panel options and reference the rates ADO by contract address or VFS path.
{% endhint %}

* **Action:** The action is the execute message we want the rates to be applied to. In the marketplace, we want the tax to be payed when the user is buying the NFT from the marketplace, so we specify it as "**Buy**". Now every time a user calls the "**Buy**" modifier, they will need to pay the 5% added tax on top of the price.
* **Local:** Indicates that the rate will be specified from the ADO itself.&#x20;
* **Description**: A small description for the rates.
* **Rate Type:** Whether the rate should be added on top of the price (tax payed by the buyer) or should be deducted from the price (royalty paid by the seller). In our case we want it to be additive.

{% hint style="warning" %}
There could be multiple addresses to receive rates. If you specify 3 addresses, and the rate is 5%, then each of the 3 addresses would receive 5% for a total of 15%  (It is not divided between the 3).
{% endhint %}

* **Recipients:** The addresses to receive this rate. We specify the address of the entity taking the tax. In our case, an address representing the art gallery that is hosting this project.

{% hint style="warning" %}
Percent is specified as a decimal. 0.01= 1%, 1=100% .
{% endhint %}

* **Value:** Specifies if the rate should be a percentage of the price or a flat amount. In our case we want a 5% rate so we select "**Percent**" and input 0.05 .

Once done, the panel should look something like this:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 6.07.59 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 6.08.08 PM (1).png" alt=""><figcaption></figcaption></figure>

Once you publish, the rates have been successfully applied.&#x20;

### Setting Permissions

Now we need to give the 2 artists permission to mint an NFT. In the **CW721** modifiers, select "**Permissioning**":

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 6.13.11 PM.png" alt=""><figcaption></figcaption></figure>

**Set the permission on the action and publish:**

{% hint style="warning" %}
In case of a whitelist (instead of a limited permission), you would need to call PermissionAction prior to setting the whitelist.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 6.21.14 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
You can now add multiple actors at a time. This was not possible at the time of making this guide. You could permission both artists in one go by clicking add item under actor.&#x20;
{% endhint %}

{% hint style="warning" %}
Action is case sensitive.
{% endhint %}

* **Action:** The execute message we are setting a permission on. In our case it is minting an NFT so we specify as "**Mint**".
* **Actor:** The address we are assigning the permission to. In our case, specify the address of the first artitst.

{% hint style="info" %}
You can also set up the permissions in a seperate **Address List** ADO and then select "Contract" in the panel options and reference the **Address List** ADO by contract address or VFS path.

It is recommended to use addresses that are yours to be able to test minting the NFTs from all the addresses later on.&#x20;
{% endhint %}

* **Local:** Indicates that the permission will be specified from the ADO itself.&#x20;
* **Limited:** Indicates the permission will be set for a number of uses. Blacklist and whitelist can also be set as permission types. For our case, limited makes sense as we want to permission the artists to call the Mint function one time.&#x20;
* **Expiration:** If there should be an expiration for the permission. For example I can whitelist an address for 1 week and after it expires, the permission will be removed automatically. We will not include one in our example.
* **Uses:** The number of times the permissioned actor is able to called the action. In our case we want it to be once.

Publish and then repeat the same thing (**from step 2)** to permission the second artist by specifying them as the actor this time.&#x20;

### Minting the NFTs

We can now start he minting process for the NFTs. First let us start with our NFT (administrator). Go to the CW721 modifiers, and select **Mint** from the list of NFTs:&#x20;

{% hint style="info" %}
I have already set up 4 NFTs on [Pinata](https://www.pinata.cloud) to be used as the **Token Identifier** for each NFT. You can learn how to set up your own nfts [here](building-your-first-nft-collection/setting-up-nft-metadata.md). You can also choose to use the same NFTs as me.

**Admin NFT:** [https://gateway.pinata.cloud/ipfs/QmNj9dWRQVqBmNyvHbJBQsX1UrpCSUiq7REcFZ5seD9Xuj](https://gateway.pinata.cloud/ipfs/QmNj9dWRQVqBmNyvHbJBQsX1UrpCSUiq7REcFZ5seD9Xuj)

**Artist 2 NFT:** [https://gateway.pinata.cloud/ipfs/QmXU5gjwv4juYRvMg7o7JsHKNja7dWdx5nRhiiKXjcMcLa](https://gateway.pinata.cloud/ipfs/QmXU5gjwv4juYRvMg7o7JsHKNja7dWdx5nRhiiKXjcMcLa)

**Artist 3 NFT:** [https://gateway.pinata.cloud/ipfs/QmTkLygY8EWYArrnLPLR2zsfECDGNDU3Yb79PkjuS9UZ6Z](https://gateway.pinata.cloud/ipfs/QmTkLygY8EWYArrnLPLR2zsfECDGNDU3Yb79PkjuS9UZ6Z)

**Collaborative NFT:** [https://gateway.pinata.cloud/ipfs/QmZdrhbNUF8bh2CjwU3MKCsd9gTejwMDjUDdbZh9K3GNbd](https://gateway.pinata.cloud/ipfs/QmZdrhbNUF8bh2CjwU3MKCsd9gTejwMDjUDdbZh9K3GNbd)
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 6.46.01 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
We are minting the collaborative NFT next as it will be minted by the same address that minted the first NFT (Admin).
{% endhint %}

Next, mint the collaborative NFT. Make sure to change the token Id and the Token Identifier.&#x20;

For the last 2 NFTs, they will be minted by artist 2 and 3 respectively. Since artists 2 and 3 will not have the App in their assets, we need to share the asset page for the CW721 with them. We can do this by copying the url of the CW721 asset page and sharing it with the each artist:

{% hint style="warning" %}
Since this is just an example build, and artist 2 and 3 are just your addresses, you can go to the CW721 asset page and switch wallets.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 7.10.42 PM.png" alt=""><figcaption></figcaption></figure>

Once you switch your wallet you will notice an "**External ADO**" label next to the name indicating that the connected address is not the owner of this ADO:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 7.15.01 PM.png" alt=""><figcaption></figcaption></figure>

Now we can mint the NFT as usual:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 7.17.39 PM.png" alt=""><figcaption></figcaption></figure>

After minting the NFTs for artist-2 and artist-3 (Using their wallets), let us connect back to the admin wallet to start configuring the NFT sales.

### Sending the NFTs to Marketplace

As we have seen in the first marketplace guide, we need to send the NFTs from the CW721 component to the marketplace component with the needed configuration in order to start a sale on the NFT.

Select **Send NFT** from the list of modifiers for the CW721:&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 6.39.02 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You can also use the VFS path to specify the recipient.
{% endhint %}

**Recipient:** The address to receive the NFT. In our case, we need to send it to the marketplace, so use that address (You can get from the asset page).&#x20;

**Token Id:** The token Id of the NFT we are sending. Use the token Id of the NFT minted by the connected wallet.

For supporting messages, select **marketplace**, then the version of your marketplace component (usually the latest versin), then **cw721-start-sale**. Specify the sale options for the NFTs :

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 6.48.00 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 6.43.23 PM.png" alt=""><figcaption></figcaption></figure>

Here is a quick rundown of the options I have selected:

**Coin Denom:** `uandr` meaning the NFT is purchased using uandr tokens

**Start Time:** `None` meaning the sale will start as soon as the NFT is sent. You can chose to have it start at a later date.

**Duration:** `604800000` which is 1 week in milliseconds.

**Price:** `2000000` which is 2000000uandr or 2 ANDR.&#x20;

**Recipient:** The address of the first artist (admin). This means that the proceeds from this NFT sale will go to that address.

{% hint style="warning" %}
For the NFTs of artist-2 and artist-3, use the same method used for executing the mint on the CW721 which is switching wallets on the CW721 asset page and then going through the same process we did above. Make sure to specify their addresses respectively as the recipient.
{% endhint %}

Lastly, we need to send the collaborative NFT to the marketplace. Here, instead of specifying the **Recipient** address as one of the artists, the recipient will be the splitter which will receive the funds from the NFT sale and split it according to the split we gave it at instantiation (33% for each artist):

{% hint style="warning" %}
The last percent will go back to the owner of the splitter which is the admin in our case.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.07.26 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
We specify the msg in JSON format which will be converted to base64 automatically.
{% endhint %}

The **msg** specifies the execute message to be called on the **Recipient** (Splitter in our case) when the funds are sent to it. As seen in the [splitter docs](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/splitter), a **Send**  execute message is used to split any sent funds by the splitter. This means that in the above configuration, we are telling the marketplace to send the proceeds of the sale to the splitter, and trigger a **Send** message automatically splitting the funds to our three artists.

## Connecting to the Embeddable

Now that the NFTs are minted, and the sales have been set up, it is time to connect our application to an embeddable. Go to the embeddables section in the web-app and select "**+New Embeddable**":

{% hint style="info" %}
Add a name and description for your embeddable.&#x20;

Make sure to use uandr as the coin denom.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.20.05 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Component addresses can be found in the assets page.
{% endhint %}

Next, select "**+Add Collection**", and add a marketplace collection. Fill it in with the required info:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.23.15 PM.png" alt=""><figcaption></figcaption></figure>

You can select preview before publishing to preview how the page will look like. Once satisfied, publish the embeddable:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.25.31 PM.png" alt=""><figcaption></figcaption></figure>

Select the three dots and then "view". This will take you to the embeddable asset page:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.26.36 PM.png" alt=""><figcaption></figcaption></figure>

Here you can find information about the deployed embeddable and most importantly the **Deployment URL.** This URL can then be shared with your audience, users and they can start buying the NFTs.&#x20;

Let us take a look at our embeddable:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.34.53 PM.png" alt=""><figcaption></figcaption></figure>

We can explore the collection:

{% hint style="info" %}
All of the NFTs are live for sale as we specified in our configurations for the sale.
{% endhint %}

You can select one of the NFTs and view the traits for it:

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.40.47 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
These traits were set up in the JSON files on pinata.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-27 at 7.32.57 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Feel free to explore the embeddable or even connect a new wallet and purchase some of the NFTs and check the balances for the artists before and after purchases.
{% endhint %}

## Conclusion

In this extended marketplace use case, we took the simple build of the marketplace and gave it a real story and use case. We went from nothing to a fully functional marketplace that would usually take months to build. This is just the tip of the iceberg of what is capable using the aOS. Now that you know the basics, time to go build some more.
