# CW20 Exchange

## ntroduction <a href="#pt6sfxm1ujzm" id="pt6sfxm1ujzm"></a>

This guide shows you how to create an Embeddable, using a CW20 Exchange application.

### Prerequisites

Before using an embeddable, you will need to have an App built to integrate into the embeddable. In this example, we will be using a Exchange App similar to the one built in the [CW20 Exchange](../ado-builder/cw20-exchange.md). So before continuing with this guide make sure you have done the following:

{% hint style="success" %}
You can use this link for the CW20 token logo in your application:&#x20;

[https://gateway.pinata.cloud/ipfs/QmUMVz3CSjwgY7hn1nobixGX56ZxgghGJRBS4KFNygnMpZ](https://gateway.pinata.cloud/ipfs/QmUMVz3CSjwgY7hn1nobixGX56ZxgghGJRBS4KFNygnMpZ)

![](<../../.gitbook/assets/Screenshot 2025-05-27 at 5.40.40 PM.png>)
{% endhint %}

* Created an Exchange App&#x20;
* Set up the exchange sale.

{% hint style="warning" %}
You can create an \*empty\* embeddable, then add content later with the Edit feature in the Embedding. If you do this, though, your Embeddable will appear empty until you insert content.
{% endhint %}

## Let's Get Started

### **Access Embeddables Page** <a href="#id-27am4ay8cn7p" id="id-27am4ay8cn7p"></a>

Connect your wallet to the web-app. Go to the dashboard, and select "**Embeddables**" (the <> button) from the sidebar on the left of the page:

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-26 at 9.34.32 PM.png" alt=""><figcaption></figcaption></figure>

Similar to our Apps in the assets page, each embeddable you create is linked to your connected address and chain. This means changing the connected wallet or chain will change the available embeddables to view.

### Setting up the Embeddable

To start a new embeddable, click on the " **+ New Embeddable**" button:

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-26 at 10.14.58 PM.png" alt=""><figcaption></figcaption></figure>

This will open up a panel that has the configurations for your embeddable. Here you specify the following:

1. **The Coin Denom:** The token micro-denomination to be used to buy from your application. Since I am using the Andromeda chain in this example, then I will use "**uandr**".
2. **App Name:** The name of embeddable application. Will be presented as header in your embeddable page.
3. **Twitter URL:** An optional url for the twitter account of the project that will be presented in the embeddable.
4. **Banner Image URL:** An optional URL pointing at a banner image which will be displayed on your embeddable. Must start with "**https://**".

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.25.22 PM.png" alt=""><figcaption></figcaption></figure>

### Adding the Type of Embeddable <a href="#iyphtk4dy7xk" id="iyphtk4dy7xk"></a>

Now we need to add the type of collection we are looking to embed. Since our App in this example uses the Exchange ADO, then we need to select the exchange embeddable. Press on "**+ Add Collection**", choose "**Exchange Embeddable**" and click “**Add Collection**” again.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.26.45 PM.png" alt=""><figcaption></figcaption></figure>

### Configuring the Marketplace Embeddable

Adding the marketplace embeddable opens a new panel to configure. Here we need to specify the following:

{% hint style="info" %}
It is recommended that you open the assets page in a separate tab to be able to copy the needed information as leaving the current page will delete your progress so far
{% endhint %}

* **Exchange Address:** The address of the Exchange component in the App.

Go back to your **Assets** tab, open the App that contains your Exchange component, then  copy the address and paste it in the embeddable panel:&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.39.07 PM.png" alt=""><figcaption></figcaption></figure>

* **CW20 Address:** The address of the CW20 component in the App. Also copy the address of your **"cw20"** ADO and paste it in the embeddable panel.
* **Exchange Name:** The consumer-facing name for this Embeddable. Name it whatever you wish!
* **Twitter URL:** An optional url for the twitter account of the project that will be presented in the embeddable.
* **Description:**  A description for your application that will be posted on the main page.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.41.54 PM.png" alt=""><figcaption></figcaption></figure>

### **Preview and Publish**

Now that he embeddable panels are set up, we can publish our embeddable. Before publishing though, we can preview the interface to make sure it looks the way we want it:

* **Click Publish,** which will invoke a preview/download popup

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* **Preview:** Click "**Open Preview**" to view your Embeddable’s preview page.
* **Name and Deploy:** If everything looks good, go back and name your new Embeddable, then hit “Publish”.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.54.19 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
You cannot have a space in the name.
{% endhint %}

Your embeddable is now live, after you approve the transaction.

### Checking the Embeddable in your Web-App. <a href="#id-6b77fa8rpwc8" id="id-6b77fa8rpwc8"></a>

Go to the Embeddables page, click the ‘three dots menu’ (…) on the upper right corner of your new Embeddable, then click VIEW to see your new Embeddable.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-27 at 8.59.59 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-26 at 10.50.08 PM.png" alt=""><figcaption></figcaption></figure>

### Viewing and Sharing your Embeddables <a href="#fg23njd2mtww" id="fg23njd2mtww"></a>

Click the "**Deployment**" link on your Embeddable to view the Embeddable’s deployed page. This link can be shared with your users to be used to interact with your Application. In our case, they would be able to buy CW20 tokens.

### Editing Embeddables <a href="#vlkzcom5k4gd" id="vlkzcom5k4gd"></a>

Clicking the Update button on your Embeddable's dropdown menu allows you to update the Embeddable’s details:

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-29 at 5.38.39 PM.png" alt=""><figcaption></figcaption></figure>
