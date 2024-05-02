# NFT Auction

## Introduction <a href="#pt6sfxm1ujzm" id="pt6sfxm1ujzm"></a>

This guide shows you how to create an Embeddable, using a CW721 Auction application.

![](<../../.gitbook/assets/0 (1).jpeg>)

### Prerequisites

Before using an embeddable, you will need to have an App built to integrate into the embeddable. In this example, we will be using a Auction App similar to the one built in the [**Creating your Frist NFT Auction House**](../ado-builder/creating-your-first-nft-auction-house.md). So before continuing with this guide make sure you have done the following:

* Created an Auction App with at leat one minted NFT
* [Set up the NFT metadata](../ado-builder/building-your-first-nft-collection/setting-up-nft-metadata.md) and have it stored somewhere (IPFS, Pinata,nft.storage)

{% hint style="warning" %}
Using an embeddable on an App that does not have any minted NFTs will show up as empty.&#x20;

You can create an \*empty\* embeddable, then add Content later with the Edit feature in the Embedding. If you do this, though, your Embeddable will appear empty until you insert content.
{% endhint %}

***

## Let's Get Started

### **Access Embeddables Page** <a href="#id-27am4ay8cn7p" id="id-27am4ay8cn7p"></a>

{% hint style="info" %}
Make sure to use the wallet that built the Auction App.
{% endhint %}

Connect your wallet to the web-app. Go to the dashboard, and select "**Embeddables**" (the <> button) from the sidebar on the left of the page.&#x20;

We will use the Andromeda chain for this example. Similar to our Apps in the assets page, each embeddable you create is linked to your connected address and chain. This means changing the connected wallet or chain will change the available embeddables to view.

### Setting up the Embeddable

To start a new embeddable, click on the " **+ New Embeddable**" button:

![](<../../.gitbook/assets/Screen Shot 2024-04-22 at 9.40.04 PM (1).png>)

This will open up a panel that has the configurations for your embeddable. Here you specify the following:

1. **The Coin Denom:** The token micro-denomination to be used to buy from your application. Since I am using the Andromeda chain in this example, then I will use "**uandr**".
2. **App Name:** The name of embeddable application. Will be presented as header in your embeddable page.
3. **Twitter URL:** An optional url for the twitter account of the project that will be presented in the embeddable.
4. **Banner Image URL:** An optional URL pointing at a banner image which will be displayed on your embeddable. Must start with "**https://**".

![](<../../.gitbook/assets/Screen Shot 2024-04-23 at 4.21.35 PM.png>)

### Adding the Type of Embeddable <a href="#iyphtk4dy7xk" id="iyphtk4dy7xk"></a>

Now we need to add the type of collection we are looking to embed. This depends on the Application that we are embedding. Since our App in this example uses the Auction ADO, then we need to select the auction embeddable. Press on "**+ Add Collection**", choose "**Auction Embeddable**" and click “**Add Collection**” again.

![](<../../.gitbook/assets/3 (1).jpeg>)

### Configuring the Auction Embeddable

Adding the auction embeddable open a new panel to configure. Here we need to specify the following:

**Auction Address:** The address of the auction component in the App whose auction sales you want to have in your embeddable interface.

{% hint style="info" %}
You can get the auction address from the assets page.&#x20;

Leaving the current page will delete your progress so far, so it is recommended to open the assets page in a new tab in your browser.

Clicking the address will automatically copy the address to your clipboard.&#x20;
{% endhint %}

Go back to your **Assets** tab, open the App that contains your cw721 Auction, then  copy the address of your auction component, then paste it in the embeddable panel:&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.11.35 AM (2).jpg" alt=""><figcaption></figcaption></figure>

**CW721 Address:** The address of the CW721 component in the App whose NFTs  are used in the auction.

Also copy the address of your **"cw721"** ADO and paste it in the embeddable panel:

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.11.13 AM (1).jpg>)

**Collection Name:** The consumer-facing name for this Embeddable. Name it whatever you wish!

**Twitter URL:** An optional url for the twitter account of the project that will be presented in the embeddable.

**Featured Token Id:**  An optional token Id of the CW721 NFT token which you wish to display as the main face of your NFT collection.

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.15.23 AM.jpg>)

<figure><img src="../../.gitbook/assets/Embeddables map (1).png" alt=""><figcaption><p>This is a visual explainer of where some builder fields align with their corresponding Embeddable</p></figcaption></figure>

### **Preview and Publish**

Now that he embeddable panels are set up, we can publish our embeddable. Before publishing though, we can preview the interface to make sure it looks the way we want it:

* **Click Publish,** which will invoke a preview/download popup

![](<../../.gitbook/assets/7 (1).jpeg>)

* &#x20;**Preview:** Click "**Open Preview**" to view your Embeddable’s preview page.

![](<../../.gitbook/assets/8 (1).jpeg>)

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM.jpg>)

* **Name and Deploy:** Go back and name your new Embeddable, then hit “Publish”.

{% hint style="info" %}
&#x20;In this example, we're calling our embeddable "UseCaseOneEmbeddable"
{% endhint %}

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 11.54.40 AM.jpg>)

Your embeddable is now live, after you approve the transaction.

### Checking the Embeddable in your Web-App. <a href="#id-6b77fa8rpwc8" id="id-6b77fa8rpwc8"></a>

Go to the Embeddables page, click the ‘three dots menu’ (…) on the upper right corner of your new Embeddable, then click VIEW to see your new Embeddable.

{% hint style="info" %}
Embeddables are similar to ADO assets as they are linked to your address and chain.&#x20;

If you login with a different address or on another chain, then this embeddable will not show.
{% endhint %}

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 11.58.35 AM.jpg>)

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.28.15 AM.jpg>)

{% hint style="warning" %}
At the bottom of the page, the ADOs which provides content for the new Embeddable you created are listed.
{% endhint %}

### Viewing and Sharing your Embeddables <a href="#fg23njd2mtww" id="fg23njd2mtww"></a>

Click the "**Deployment**" link on your Embeddable to view the Embeddable’s deployed page. This link can be shared with your users to be used to interact with your Application. In our case, they would be able to bid on the NFTs.

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 12.01.28 PM.jpg" alt=""><figcaption></figcaption></figure>

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM (1).jpg>)

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.50 AM.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.30.34 AM.jpg" alt=""><figcaption></figcaption></figure>

### Editing Embeddables <a href="#vlkzcom5k4gd" id="vlkzcom5k4gd"></a>

Clicking the Update button on your Embeddable's dropdown menu allows the owner to update the Embeddable’s details:

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 12.06.51 PM.jpg>)

You can update content and parameters of your embeddable, then publish the changes if you wish:

#### &#x20;<img src="../../.gitbook/assets/embed (1).png" alt="" data-size="original"> <a href="#i85tgcqolf4i" id="i85tgcqolf4i"></a>

### Conclusion <a href="#ucf4h3okogm4" id="ucf4h3okogm4"></a>

Congratulations on embedding your CW721 Auction!  Now people can visit your embeddable and start bidding on some NFTs.
