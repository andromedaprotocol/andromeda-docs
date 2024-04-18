# NFT Auction Marketplace

In this guide we will be looking to build an NFT acution marketplace using the Andromeda App builder. The goal of this guide is to introduce you to the different concepts of the app builder and not the App itself.&#x20;

{% hint style="warning" %}
We have already build this app in a previous guide using the ADO builder where we go through each of the steps in detail. If unfamiliar with the **Auction Marketplace App**, it is recommended to go through the ADO builder guides first.&#x20;
{% endhint %}

Open a blank canvas in the **App Builder** and let's get started:

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-07 at 5.46.26 PM.png" alt=""><figcaption><p>App Builder Blank Canvas</p></figcaption></figure>

Before we start building, let us cover some of the basics of the App Builder. On the left side of the screen in the following panel:

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-09 at 5.29.01 PM.png" alt=""><figcaption><p>App Builder </p></figcaption></figure>

* The search bar can be used to search for a specific ADO/Module
* Pressing "**ADOs**" will show you all the different ADOs available to be added
* Pressing "**Modules**" will show you all the different modules available to be added
* Pressing "**Templates**" will show you all the templates available to be loaded in
* Shortcuts are the keyboard shortcuts for faster building

For the Auction **Marketplace App**, you will need the following components:

* **CW721** component to mint the NFTs
* **Auction** component to configure the auctions&#x20;

Unlike the ADO builder, we will be adding all the components before we start configuring any of them. Start by adding the **CW721** and **Auction** components by selecting them from the list of available ADOs:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-03 at 8.49.01 PM.png" alt=""><figcaption><p>Adding the ADO Components</p></figcaption></figure>

When you hover over a panel, a set of options will appear above it. These options are the following:

* **Delete:** Deletes the panel.
* **Duplicate:** Makes an exact replica of the panel.
* **Download:** Dowload the information of the panel. This will download a file to your device which can be shared with others, or kept to be loaded again in the future.For example, a user sets up a panel to mint 15 NFTs with 10 attributes each. This would probably take 30-60 minutes of work. What if they realize after publishing that there is a problem with the app? That would require them to redo the 30-60 minutes of work to set up the mint panel. Another case where downloading is usefull, is when you want to use the same panel information in more than one app. For example, a user sets up a splitter panel that splits funds to 30 addresses. They might want to use the same splitter for another app that they create. Instead of redoing the work from scratch they can just load the information to the panel.&#x20;
* **Load:** Loads the downloaded panel information.

Now that we have all the components needed, we can start configuring them. Let us start with the CW721 panel:

<figure><img src="../../.gitbook/assets/Screen Shot 2023-04-26 at 5.17.08 PM.png" alt=""><figcaption><p>Example CW721 Configuration</p></figcaption></figure>

{% hint style="warning" %}
Use your own address for the minter field of the CW721&#x20;
{% endhint %}

The name of the panel is displayed on the top right and can be changed by pressing the pencil icon. Each panel has a set of connections to it:

1. Two connections on the top and bottom represented by blue circles. These are common to all panels and are used to connect this panel to a field of another panel.&#x20;
2. Two connections on the left and right of a **field** represented by purple circles. This means that this field accepts another panel as a connection.&#x20;

Next, let us configure the auction component. We want to authorize our **CW721** to be able to send NFTs to the auction. Select "**Add Item**" under "**Authorized token Addresses**" and then you can simply connect the CW721 panel to this field as shown below:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-03 at 8.54.58 PM.png" alt=""><figcaption><p> Auction Configuration</p></figcaption></figure>

{% hint style="warning" %}
You can still use the address/name of the CW721 to reference it instead of using a connection.&#x20;
{% endhint %}

The App is now complete. Go ahead and publish. Make sure to give the App a suitable name:

{% hint style="warning" %}
The App name can be changed from the beginning. It is found in the top left of the screen next to the Andromeda logo and is "untitled" by default.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-03 at 8.56.24 PM.png" alt=""><figcaption><p>Assigning a Name to the App</p></figcaption></figure>

Once you publish, you are taken to the assets page where you can view and interact with the App.

## Building the App Using a Template

We can use an Andromeda Template to build the same App but with less effort. Open a new blank canvas in the App builder and from the templates, select **Auction Based Marketplace:**

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-04 at 5.15.16 PM.png" alt=""><figcaption><p>Auction Marketplace Template</p></figcaption></figure>

The template will load all the required components for the app along with the connections. You still need to configure the fields yourself, as each user will want different values depending on their use case. Andromeda will provide a wide range of templates covering the most popular use cases that can be build using our ADOs.&#x20;
