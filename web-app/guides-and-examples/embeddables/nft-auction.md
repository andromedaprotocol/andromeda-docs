# NFT Auction

## Introduction <a href="#pt6sfxm1ujzm" id="pt6sfxm1ujzm"></a>

This guide shows you how to create an Embeddable, using a CW721 Auction application.

![](<../../.gitbook/assets/0 (1).jpeg>)

### Prerequisites

Before using an embeddable, you will need to have an App built to integrate into the embeddable. In this example, we will be using a Auction App similar to the one built in the [**Creating your Frist NFT Auction House**](../ado-builder/creating-your-first-nft-auction-house.md). So before continuing with this guide make sure you have done the following:

* Created an Auction App with at leat one minted NFT
* Set up the NFT metadata and have it stored somewhere (IPFS, Pinata,nft.storage)

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

1. **The Coin Denom:** The token micro-denomination to be used to buy from your application.&#x20;

### **3. Enter the coin denomination which your embeddable (and its contents) will accept as payment, **<mark style="color:yellow;">**using the micro-denomination to execute.**</mark>&#x20;

### _(We are using the micro-denomination '<mark style="color:yellow;">uandr</mark>' for this example, since in this instance, we are creating this Embeddable on the Andromeda testnet)._

### **4. Name the Embeddable.** Add optional Twitter or banner images.

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 9.56.27 AM.jpg>)

### **5. Add A Collection To Your Embeddable:** Choose "Auction Embeddable" (for this example) and click “Add Collection”.  <a href="#iyphtk4dy7xk" id="iyphtk4dy7xk"></a>

![](<../../.gitbook/assets/3 (1).jpeg>)

### >> Configuration:&#x20;

### **6. Go back** to your **Assets** tab, open the App that contains your cw721 Auction, then  **COPY** the address of your "**auction@**" ADO. &#x20;

### <mark style="color:yellow;">Note</mark>: Clicking the address will automatically copy the address to your clipboard.&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.11.35 AM (2).jpg" alt=""><figcaption></figcaption></figure>

### 7. Also **COPY** the address of your **"cw721@"** ADO. Have both addresses available for the next steps.&#x20;

### <mark style="color:yellow;">Note</mark>: Clicking the address will automatically copy the address to your clipboard.



![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.11.13 AM (1).jpg>)

### **8. Go back to your** **Embedding**, then enter the info you just copied:

{% hint style="warning" %}
\- **Auction Address** is the address of the Auction Component you're using as content for your Embeddable. Input the desired component's address and the item's token identifier. In this example, we use the contract address from “auction-1-test1"



\- **CW721 Address** is the address of the CW721 Component you're using as content for your Embeddable. **Always use the one that minted the items you wish to display.** In this example, we use the contract address from “CW721-1-test1" since it minted the NFT that we want this embed to display!



**-Collection Name** is the consumer-facing name for this Embeddable. Name it whatever you wish!
{% endhint %}

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.15.23 AM.jpg>)

<figure><img src="../../.gitbook/assets/Embeddables map (1).png" alt=""><figcaption><p>This is a visual explainer of where some builder fields align with their corresponding Embeddable</p></figcaption></figure>

### **9. Click Publish,** which will invoke a preview/download popup

![](<../../.gitbook/assets/7 (1).jpeg>)

### 10. Preview: Click "Open Preview" to view your Embeddable’s preview page. <a href="#icby1oumoe6r" id="icby1oumoe6r"></a>

![](<../../.gitbook/assets/8 (1).jpeg>)

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM.jpg>)

### 11. Name and Deploy: Go back and name your new Embeddable, then hit “Publish”  <a href="#hb24vi20cs3d" id="hb24vi20cs3d"></a>

&#x20;\- in this example, we're calling our embeddable "UseCaseOneEmbeddable"

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 11.54.40 AM.jpg>)

### 12. Click “Estimate” to simulate your transaction. <a href="#hd9ejq631pio" id="hd9ejq631pio"></a>

![](<../../.gitbook/assets/11 (1).jpeg>)

### 13. Broadcast the transaction to the chain <a href="#m0b8zrve32id" id="m0b8zrve32id"></a>

![](<../../.gitbook/assets/12 (1).jpeg>)

### 14. Approve the transaction on your Keplr wallet. <a href="#pk5qfbxbmy5h" id="pk5qfbxbmy5h"></a>

![](<../../.gitbook/assets/13 (1).jpeg>) ![](<../../.gitbook/assets/14 (1).jpeg>)

### 15. Test: Check the Embeddable in your web App. <a href="#id-6b77fa8rpwc8" id="id-6b77fa8rpwc8"></a>

Go to the Embeddables page, click the ‘three dots menu’ (…) on the upper right corner of your new Embeddable, then click VIEW to see your new Embeddable

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 11.58.35 AM.jpg>)

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.28.15 AM.jpg>)

{% hint style="warning" %}
**Note, at the bottom of the page, the ADO which provides content for the new Embeddable you created.**
{% endhint %}

### 16. Looking at your Embeddables <a href="#fg23njd2mtww" id="fg23njd2mtww"></a>

Click the "Deployment" link on your Embeddable to view the Embeddable’s deployed page

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 12.01.28 PM.jpg" alt=""><figcaption></figcaption></figure>

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM (1).jpg>)

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.23.50 AM.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2023-12-15 at 10.30.34 AM.jpg" alt=""><figcaption></figcaption></figure>

### 17. Editing Embeddables <a href="#vlkzcom5k4gd" id="vlkzcom5k4gd"></a>

Clicking the Update button on your Embeddable's dropdown menu allows the owner to update the Embeddable’s details

![](<../../.gitbook/assets/Screenshot 2023-12-15 at 12.06.51 PM.jpg>)

#### You can update content and parameters of your embeddable, then publish the changes if you wish.  <a href="#id-9ahwzvo8nyc6" id="id-9ahwzvo8nyc6"></a>

#### &#x20;<img src="../../.gitbook/assets/embed (1).png" alt="" data-size="original"> <a href="#i85tgcqolf4i" id="i85tgcqolf4i"></a>

### Conclusion <a href="#ucf4h3okogm4" id="ucf4h3okogm4"></a>

Congratulations on embedding your CW721 Auction! &#x20;
