# Creating your First NFT Auction House

In our last guide “[**Building your First NFT Collection**](building-your-first-app.md)”, we built a simple Andromeda App with one CW721 component and minted some NFTs using the execute messages. In this example, you will create an private NFT auction house for your collection, enabling you to sell them through customized auctions.

To start off, you will be importing our last build into the web-app. To do so, we select “**Saved File**” in the ADO Builder instead of a Empty Project. Then select the .flex file that you had downloaded previously:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-15 at 3.37.23 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
If you haven't exported the last build or gone through the previous example, please follow the steps in the last example again, but stop before publishing the App.
{% endhint %}

Change the name of the app, and don't forget to update the minter address **if** you've switched to a different chain or wallet. After that, you can proceed with adding the auction component, which will enable you to auction the NFTs that are minted. Add an “**Auction**” component to the app and give it a suitable name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-18 at 6.14.27 PM.png" alt=""><figcaption></figcaption></figure>

The Auction component has three fields:

{% hint style="warning" %}
Make sure to reference the NFT component correctly ".**/\<component-name>**". Since my NFT component is named NFTs, then the reference will be "**./NFTs**"

Keeping the **Authorized token addresses** field empty means the auction accepts NFTs from any CW721 ADO. This option is for open auction applications where any user can sell their NFT using your application. In this example, we are going for a private auction intended to just sell your own NFT collection.
{% endhint %}

* **Authorized token addresses**: The addresses of the CW721 ADOs that are allowed to send NFTs to be auctioned. This is to prevent unwanted NFTs to be sent to the auction by random users. We want our NFT contract that we set up before to be authorized, so we can reference it by its local name ".**/NFTs**"
* **Authorized Sale Type:** Allows us to define a CW20 ADO  whose tokens can be set as the bidding token for the NFT sales. This would restrict the CW20 ADOs allowed to be set to the one defined here. In case we just select native, then we can set any CW20 as the purchasing token. Using a native token is always accepted. This will be defined later on when we configure auction parameters. So for now we select the **Native** option.
* **Modules:** Allows users to add modules to the auction. For now, you do not need any modules, so you can keep it empty.

{% hint style="warning" %}
Modules are currently disabled, but will be available in our next release.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-18 at 6.36.13 PM.png" alt=""><figcaption></figcaption></figure>

Publish the app to chain so we can start setting up the auctions:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-15 at 3.44.51 PM.png" alt=""><figcaption></figcaption></figure>

We need to mint the NFTs we are looking to sell. In the CW721 component, select the **Batch Mint** execute and mint 3 tokens. Once done, publish to chain.

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

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-18 at 6.46.06 PM.png" alt=""><figcaption></figcaption></figure>

Here we can configure our auction options:

1. Specify if we want to use a CW20 token as the bidding token. I will not check the box as I intend to use native.
2. Specify the coin denom accepted for bidding. Since I am building on the Stargaze chain, and I want to use a native tokens as the bidding token, I will specify **ustars**.
3. Specify when the auction starts. This is specified through an [epoch time](https://www.google.com/url?q=https://www.epochconverter.com\&sa=D\&source=editors\&ust=1684253771700484\&usg=AOvVaw1ibK4NOXubSN0uNcYHSI6F) in milliseconds. Selecting none means the auction will start immediately after the message is published. I will use that option.
4. Specify the end time of the auction as an epoch time in milliseconds. I want the auction to run for 2 hours. Usin the date and time selector, we can easily specify the end time.
5. Specify the minimum bid accepted. This field is optional. I do not want a minimum bid, so I select No Minimum Bid.
6. Specify the recipient which is the address to receive the funds from a succesfull NFT sale. I will select none as it will default to my address (The sender of the NFT).
7. Specify a whitelist of addresses allowed to bid on the NFT. This field is optional. I will not add a whitelist for this example.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-18 at 6.56.04 PM.png" alt=""><figcaption></figcaption></figure>

Once done with configuring the fields, publish the message to chain. People can now bid on the NFT you just sent to auction (When the specified start time is reached). Since you sent the NFT to the auction component, it will be used to bid on the NFT.&#x20;

We have succesfully set up one NFT auction. You can do the same for each minted NFT. You can also use the **Multi Execute** option, to set up multiple messages at a time. This would allow you to send multiple NFTs to the auction in one go.

{% hint style="info" %}
If you want to try bidding on the NFT you just put up for auction, check our "[Using CLI to Interact with Apps](../using-cli-to-interact-with-apps.md)" section.

If you want to make a custom interface for users to bid on the NFTs, check out our "[Embeddables](../embeddables/)" guide.

If you are interested in using queries on your App components, check out our [Using Queries ](../../learning-the-basics/using-queries.md)section to learn about that process.

&#x20;If you are interested in using CW20 tokens as the bidding token, you can check out the "[Auction Using CW20 Tokens](auction-using-cw20-tokens.md)" example.
{% endhint %}

Next, we will be going through building some of the popular dApps in the space starting from an [NFT Marketplace App](nft-marketplace-app.md).
