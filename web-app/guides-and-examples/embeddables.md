# Embeddables

## Building An Embeddable: A General Overview <a href="#ok8t1bh7enzj" id="ok8t1bh7enzj"></a>

Embeddables currently provide a base working example of how you can utilize the ADO based apps you have constructed from within a web application.&#x20;

Although they are slightly simplistic interfaces they show all of neccessary interactions with the apps, such as data retrieval, wallet connection, and triggering actions with the app.&#x20;

These foundations for these micro-sites are open source and available at: [https://github.com/andromedaprotocol/embeddable-marketplace-demo](https://github.com/andromedaprotocol/embeddable-marketplace-demo) &#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-10-09 at 9.13.22 AM.jpg" alt="" width="563"><figcaption></figcaption></figure>

### Benefits of Embeddables <a href="#wlwom5a2wigo" id="wlwom5a2wigo"></a>

* **Plug-and-Play**: Embed a widget onto your website with your content using minimal coding.
* **Customizable:** Adjust to match your website's aesthetics with the design tools. (Not Yet Available)
* **Versatile:** Suitable for auctions, marketplaces, crowdfunding, and other Apps that you create.

***

***

## Introduction <a href="#pt6sfxm1ujzm" id="pt6sfxm1ujzm"></a>

This guide shows you how to create an Embeddable, using a CW721 Auction as example content.

![](<../.gitbook/assets/0 (1).jpeg>)

{% hint style="warning" %}
**Prerequisites - You Need These To Proceed - Wallet/Tokens/Content**

* Wallet: A Keplr wallet connected to the Galileo testnet, filled with ANDR tokens. \[[Guide](https://docs.andromedaprotocol.io/guides/guides-and-examples/setting-up-your-wallet)].&#x20;
* Tokens: You need to join the \[[Andromeda Discord Channel](https://discord.gg/5HHz37zGdJ)] to get uANDR test tokens from the `Public > Faucet-pub` sub-channel, using the following command with your new ANDR wallet address: <mark style="color:green;">`!request andr1x....asdf`</mark>
* Content: A CW721 Auction Component \[[Guide](https://docs.andromedaprotocol.io/guides/guides-and-examples/ado-builder/creating-your-first-nft-auction-house)] \*with at least one CW721 token in it.\*
{% endhint %}

{% hint style="danger" %}
Note: An auction Embeddable without CW721 tokens in it will appear empty.&#x20;

For content (e.g.-an NFT, a CW20 token, etc) to appear in an Embeddable frame, your content component (e.g. - an Auction component, a cw20 Exchange component, etc) must contain a minimum of one minted token.

#### Yes, you can create an \*empty\* embeddable, then add Content later with the Edit feature in the Embedding. If you do this, though, your Embeddable will appear empty until you insert content.&#x20;
{% endhint %}

***

## Let's Get Started

### **1. Access Embeddables Page:** <a href="#id-27am4ay8cn7p" id="id-27am4ay8cn7p"></a>

**-** Log into your Andromeda App with your Keplr wallet, \
then go to the dashboard, and select "Embeddables" (the <> button) from the sidebar on the left of the page. \
\
\- We will use the Andromeda chain for this example, but remember that **each chain has its own Embeddables**. After you create them, you simply switch chains to see each chain's embeddables.

{% hint style="info" %}
**NOTE: If unsure about connecting to the Andromeda web App, see the “Setting up your wallet” \[**[**Guide**](https://docs.andromedaprotocol.io/guides/guides-and-examples/setting-up-your-wallet)**].**
{% endhint %}

### **2. Click “+ New Embeddable”.**

![](<../.gitbook/assets/1 (1).jpeg>)

### **3. Enter the coin denomination which your embeddable (and its contents) will accept as payment, **<mark style="color:yellow;">**using the micro-denomination to execute.**</mark>&#x20;

### _(We are using the micro-denomination '<mark style="color:yellow;">uandr</mark>' for this example, since in this instance, we are creating this Embeddable on the Andromeda testnet)._

### **4. Name the Embeddable.** Add optional Twitter or banner images.

![](<../.gitbook/assets/Screenshot 2023-12-15 at 9.56.27 AM.jpg>)

### **5. Add A Collection To Your Embeddable:** Choose "Auction Embeddable" (for this example) and click “Add Collection”.  <a href="#iyphtk4dy7xk" id="iyphtk4dy7xk"></a>

![](<../.gitbook/assets/3 (1).jpeg>)

### >> Configuration:&#x20;

### **6. Go back** to your **Assets** tab, open the App that contains your cw721 Auction, then  **COPY** the address of your "**auction@**" ADO. &#x20;

### <mark style="color:yellow;">Note</mark>: Clicking the address will automatically copy the address to your clipboard.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-12-15 at 10.11.35 AM (2).jpg" alt=""><figcaption></figcaption></figure>

### 7. Also **COPY** the address of your **"cw721@"** ADO. Have both addresses available for the next steps.&#x20;

### <mark style="color:yellow;">Note</mark>: Clicking the address will automatically copy the address to your clipboard.



![](<../.gitbook/assets/Screenshot 2023-12-15 at 10.11.13 AM (1).jpg>)

### **8. Go back to your** **Embedding**, then enter the info you just copied:

{% hint style="warning" %}
\- **Auction Address** is the address of the Auction Component you're using as content for your Embeddable. Input the desired component's address and the item's token identifier. In this example, we use the contract address from “auction-1-test1"



\- **CW721 Address** is the address of the CW721 Component you're using as content for your Embeddable. **Always use the one that minted the items you wish to display.** In this example, we use the contract address from “CW721-1-test1" since it minted the NFT that we want this embed to display!



**-Collection Name** is the consumer-facing name for this Embeddable. Name it whatever you wish!
{% endhint %}

![](<../.gitbook/assets/Screenshot 2023-12-15 at 10.15.23 AM.jpg>)

<figure><img src="../.gitbook/assets/Embeddables map (1).png" alt=""><figcaption><p>This is a visual explainer of where some builder fields align with their corresponding Embeddable</p></figcaption></figure>

### **9. Click Publish,** which will invoke a preview/download popup

![](<../.gitbook/assets/7 (1).jpeg>)

### 10. Preview: Click "Open Preview" to view your Embeddable’s preview page. <a href="#icby1oumoe6r" id="icby1oumoe6r"></a>

![](<../.gitbook/assets/8 (1).jpeg>)

![](<../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM.jpg>)

### 11. Name and Deploy: Go back and name your new Embeddable, then hit “Publish”  <a href="#hb24vi20cs3d" id="hb24vi20cs3d"></a>

&#x20;\- in this example, we're calling our embeddable "UseCaseOneEmbeddable"

![](<../.gitbook/assets/Screenshot 2023-12-15 at 11.54.40 AM.jpg>)

### 12. Click “Estimate” to simulate your transaction. <a href="#hd9ejq631pio" id="hd9ejq631pio"></a>

![](<../.gitbook/assets/11 (1).jpeg>)

### 13. Broadcast the transaction to the chain <a href="#m0b8zrve32id" id="m0b8zrve32id"></a>

![](<../.gitbook/assets/12 (1).jpeg>)

### 14. Approve the transaction on your Keplr wallet. <a href="#pk5qfbxbmy5h" id="pk5qfbxbmy5h"></a>

![](<../.gitbook/assets/13 (1).jpeg>) ![](<../.gitbook/assets/14 (1).jpeg>)

### 15. Test: Check the Embeddable in your web App. <a href="#id-6b77fa8rpwc8" id="id-6b77fa8rpwc8"></a>

Go to the Embeddables page, click the ‘three dots menu’ (…) on the upper right corner of your new Embeddable, then click VIEW to see your new Embeddable

![](<../.gitbook/assets/Screenshot 2023-12-15 at 11.58.35 AM.jpg>)

![](<../.gitbook/assets/Screenshot 2023-12-15 at 10.28.15 AM.jpg>)

{% hint style="warning" %}
**Note, at the bottom of the page, the ADO which provides content for the new Embeddable you created.**
{% endhint %}

### 16. Looking at your Embeddables <a href="#fg23njd2mtww" id="fg23njd2mtww"></a>

Click the "Deployment" link on your Embeddable to view the Embeddable’s deployed page

<figure><img src="../.gitbook/assets/Screenshot 2023-12-15 at 12.01.28 PM.jpg" alt=""><figcaption></figcaption></figure>

![](<../.gitbook/assets/Screenshot 2023-12-15 at 10.23.20 AM (1).jpg>)

<figure><img src="../.gitbook/assets/Screenshot 2023-12-15 at 10.23.50 AM.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screenshot 2023-12-15 at 10.30.34 AM.jpg" alt=""><figcaption></figcaption></figure>

### 17. Editing Embeddables <a href="#vlkzcom5k4gd" id="vlkzcom5k4gd"></a>

Clicking the Update button on your Embeddable's dropdown menu allows the owner to update the Embeddable’s details

![](<../.gitbook/assets/Screenshot 2023-12-15 at 12.06.51 PM.jpg>)

#### You can update content and parameters of your embeddable, then publish the changes if you wish.  <a href="#id-9ahwzvo8nyc6" id="id-9ahwzvo8nyc6"></a>

#### &#x20;<img src="../.gitbook/assets/embed (1).png" alt="" data-size="original"> <a href="#i85tgcqolf4i" id="i85tgcqolf4i"></a>

### Conclusion <a href="#ucf4h3okogm4" id="ucf4h3okogm4"></a>

Congratulations on embedding your CW721 Auction! &#x20;
