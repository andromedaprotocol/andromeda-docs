# Creating your First NFT Auction House

In our last guide “[**Building your First NFT Collection**](building-your-first-app.md)”, we built a simple Andromeda App with one CW721 component and minted some NFTs using the execute messages. In this example, you will create an NFT auction house for your collection, enabling you to sell them through customized auctions.

To start off, you will be importing our last build into the web-app. To do so, we select “**Saved File**” in the ADO Builder instead of a Empty Project. Then select the .flex file that you had downloaded previously:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-15 at 3.37.23 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
If you haven't exported the last build or gone through the previous example, please follow the steps in the last example again, but stop before publishing the App.
{% endhint %}

Change the name of the app, and don't forget to update the minter address **if** you've switched to a different chain or wallet. After that, you can proceed with adding the auction component, which will enable you to auction the NFTs that are minted. Add an “**Auction**” component to the app and give it a suitable name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-15 at 3.40.12 PM.png" alt=""><figcaption></figcaption></figure>

The Auction component has two optional fields:

* **Authorized token addresses**: The addresses of the CW721 ADOs that are allowed to send NFTs to be auctioned. This is to prevent unwanted NFTs to be sent to the auction by random users. We will set this later, so we can keep it as empty.
* **Modules:** Allows users to add modules to the auction. For now, you do not need any modules, so you can keep it empty.

{% hint style="warning" %}
Modules are currently disabled, but will be available in our next release.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 3.56.03 PM.png" alt=""><figcaption></figcaption></figure>

Publish the app to chain so we can start setting up the auctions:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-15 at 3.44.51 PM.png" alt=""><figcaption></figcaption></figure>

Before starting any auctions, we need to authorize our CW721 ADO to be able to send NFTs to the auction component. To do so select the "**Authorize Token Contract**" from the list of execute messages for the Auction component. Insert the address of the CW721 (which you can get from the assets page) in the **Addr** field. For expiration, select **Option 2** which means there is no expiration for this authorization. Finally publish:

{% hint style="warning" %}
Only the owner of the Auction ADO is able to authorize a CW721 address.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-03 at 6.35.54 PM.png" alt=""><figcaption></figcaption></figure>

Now that the CW721 has been authorized, we will need to mint some NFTs to auction . In the CW721 component, select the **Batch Mint** execute and mint 3 tokens. Once done, publish to chain.

{% hint style="info" %}
You can download the flex file for the batch minting before publishing to be imported later on.
{% endhint %}

To start an auction for an NFT, you need to send the NFT to the auction and attach the appropriate message using the **SendNft** execute message for the CW721 component. This message is similar to a **TransferNft** (Which transfers ownership of an NFT)  message, but is used specifically to send an NFT to another ADO/contract instead of a user address and instructs that ADO/contract to initiate an action, in this case starting an auction.

Select the **SendNft** execute message:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 4.30.52 PM.png" alt=""><figcaption></figcaption></figure>

The first required field is the **Recipient Address**. This is the address to send the NFT to. In our case, we want to send it to the **Auction** component in our app.

{% hint style="info" %}
We can get the address from the assets page under **Address** in the Auction panel.
{% endhint %}

Next, you need to provide the token Id of the NFT you want to send. For the first auction, send the first NFT you minted (In my case the token Ids were 1,2,3).

{% hint style="warning" %}
&#x20;You might notice that the field name here is Token/NFT Name and not Token Id. This is a mistake in our panel that we will be fixing very soon.
{% endhint %}

Finally you need to select the message to attach along with our NFT. Select “**Auction**” from the “**Custom Message**” options, then select “**CW721 start auction**”:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 4.33.38 PM.png" alt=""><figcaption></figcaption></figure>

Here we can configure our auction options:

1. Specify the coin denom accepted for bidding. Since I am building on the Stargaze chain, I will use ustars.
2. Specify the duration of the auction in milliseconds. I want the auction to run for 2 hours.
3. Specify when the auction starts. This is specified through an [epoch time](https://www.google.com/url?q=https://www.epochconverter.com\&sa=D\&source=editors\&ust=1684253771700484\&usg=AOvVaw1ibK4NOXubSN0uNcYHSI6F) in milliseconds. The value I use here will be a time in the past for you which is not allowed for a start time. Choose a value that is not too far in the future.
4. Specify the minimum bid accepted. This field is optional. I do not want a minimum bid, so I select No Minimum Bid.
5. Specify a whitelist of addresses allowed to bid on the NFT. This field is optional. I will not add a whitelist for this example.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 4.58.22 PM.png" alt=""><figcaption></figcaption></figure>

Once done with configuring the fields, publish the message to chain. People can now bid on the NFT you just sent to auction (When the specified start time is reached). Since you sent the NFT to the auction component, it will be used to bid on the NFT.&#x20;

{% hint style="info" %}
If you want to try bidding on the NFT you just put up for auction, check our "[Using CLI to Interact with Apps](../using-cli-to-interact-with-apps.md)" section.

If you want to make a custom interface for users to bid on the NFTs, check out our "[Embeddables](../embeddables.md)" guide.

If you are interested in using queries on your App components, check out our [Using Queries ](../using-queries.md)section to learn about that process.
{% endhint %}

Next, we will be going through building some of the popular dApps in the space starting from an [NFT Marketplace App](nft-marketplace-app.md).
