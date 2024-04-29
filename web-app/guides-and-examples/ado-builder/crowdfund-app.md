# Crowdfund App

In this guide, we will be building a Crowdfunding application using the Andromeda ADOs.

### Defining the App

Before we begin building the App in the ADO Builder, let's define its features and functionality. We will assume the following:

* A user/team is looking is looking to crowdfund their project through the sale of NFTs
* The funds obtained after the sale will be split to different addresses where the first address is given 80% of the funds and the second address 20%
* When the crowdfunding sale ends, the funds should be **automatically** split to the correct destinations

### Defining the Components

To create the app we described above, we need the following components:

* [CW721](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to mint NFTs
* [Crowdfund](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/crowdfund) component to start the crowdfund sales
* [Splitter](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/splitter) component to split the funds automatically when the sale ends

The workflow of the App can be described as the following:&#x20;

* Build the app and publish to the chain
* Mint the NFTs&#x20;
* Start a sale on the NFTs
* End the sale which sends the funds to the splitter
* Splitter receives funds and splits to the designated addresses

## Building the App

Open a new empty project in the ADO Builder and start by giving the App an appropriate name:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.34.36 PM.png" alt=""><figcaption><p>Assinging a Name to the App</p></figcaption></figure>

Now add a **CW721** component to the build. One thing to keep in mind here is that the **Crowdfund** component needs to be the minter and owner of the NFTs in order to start a sale on the NFTs later on.&#x20;

Knowing this, fill in the CW721 panel and specify the minter as the crowdfund component. As we have seen in previous builds, the minter can be referenced using either the address or the local VFS name of the component in the app.&#x20;

In the panel below, I used "**./Crowdfund**" as the **Minter Address** as I plan to name the crowdfund component "**Crowdfund**" when I add it to the app. You can use any name as long as the name of the crowdfund component is the same:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.37.57 PM.png" alt=""><figcaption></figcaption></figure>

Next, add the crowdfund component to the app. For the crowdfund, you need to specify the CW721 that will be sending the NFTs to the crowdfund component which is the CW721 component you added earlier. Again we reference it by the local VFS name:&#x20;

{% hint style="info" %}
You can check the "Mint after sale allowed?" to be able to mint NFTs after the crowdfund sale is over.

For the Token Address, I have put "**./Crowdfund-Nfts**" since this is the name of CW721 component in my app.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.39.50 PM.png" alt=""><figcaption></figcaption></figure>

Finally, add the splitter component to the app. For the splitter, you need to specify the **Recipients** of the funds which will be 2 addresses of your choice. For the **Recipients** field, select "Add Item" which will add the first recipient:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-19 at 3.49.20 PM.png" alt=""><figcaption></figcaption></figure>

We specified earlier that we want to send 80% of the funds to one address and the remaining 20% to another. For the first recipient, specify 0.8 for the percentage (meaning 80%). Then insert the address you want the funds to go to in the **Address** field. Keep the other fields as None.

Once done, add another recipient by selecting "**+ Add Item**", and repeat the same steps. Make sure to specify the percentage as 0.2 for the second recipient and to specify the **Address** as the address you want the 20% to go to.

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.51.49 PM.png" alt=""><figcaption></figcaption></figure>

The App is now complete. Go ahead and publish to chain:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.53.11 PM.png" alt=""><figcaption><p>The App in Assets Page</p></figcaption></figure>

## Interacting with the App

### Minting the NFTs

Before you can start the sale, you need to mint some NFTs to sell. You can mint these NFTs using the Crowdfund component itself. Select "Mint" from the list of execute messages for the Crowdfund component:

{% hint style="danger" %}
To be eligible for the crowdfund sale, the owner of the NFT needs to be the Crowdfund component.&#x20;

You can keep the "**Token/NFT Owne**r" field empty. By default when a mint is called by the crowdfund, it assigns itself as the owner of the minted NFT.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-16 at 5.56.32 PM.png" alt=""><figcaption></figcaption></figure>

For each NFT you want to mint, select "**Add Item**" and provide the NFT data. You can mint up to 100 NFTs at a time. Mint as many as you want and then publish.

### Starting the Crowdfund Sale

Now that everything is in place, you can start the sale on the NFTs. Select "**Start Sale"** from the list of execute messages for the crowdfund component:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-19 at 4.06.38 PM.png" alt=""><figcaption></figcaption></figure>

The fields are the following:

{% hint style="warning" %}
Make sure to select the **End Time** to be in the future.
{% endhint %}

* **End Time:** Required field that specified when the sale ends meaning users can no longer purchase NFTs for the Crowdfund. Choose any date in the future.
* **Max Tokens Per Wallet:** An optional field that specifies the maximum amount of NFTs one address is able to purchase from the sale. Defaults to 1.&#x20;
* **Minimum Tokens Being Sold:** Required field specifying the minimum amount of tokens that need to be sold in order for the crowdfund to succeed. In case this number is not met, refunds will be issued to all the buyers and the NFTs will be burned. Choose a number less than or equal to the number of NFTs that have been minted for the sale.
* **Price Per Token/NFT:** The amount and denomination of the funds required to purchase one NFT from the sale. I use ustars here as I am creating my app on the stargaze chain.&#x20;
* **Start Time:** The time to start the sale. Selecting **None** would mean the sale will start immediatly.
* **Recipient**: You need to specify who receives the funds from the sale. In this case, you want the splitter to receive the funds to split them to the desired addresses that we have set up earlier (80/20 split). Since the splitter and crowdfund components belong to the same app, we can reference the splitter by name (You can use the contract address as well).

For the message, you want the splitter to send the funds to the addresses. We can use the "[Send](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/splitter#send)" execute of the splitter to do that.&#x20;

#### Attaching Messages to Recipients

Whenever you have the option to specify a recipient as an ADO (Like in our case here), you are given the option to attach a message along with any funds that will be transfered to the ADO. This message will be either a **base64** encoded message or a JSON message of one of the execute messages of the receiving ADO. Whenever the ADO receives the funds, the message is automatically called and executed (Assuming the caller has the authority to call the attached message). &#x20;

For a splitter, we are interested in the [Send](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/splitter#send) execute messge. This execute takes the sent funds and splits them to the addresses assigned to the splitter which in our case is the 2 addresses we specified earlier with the 80 20 split . This means that every time the Crowdfund sends funds to this recipient, it will execute the **Send** excecute message automatically.

{% hint style="warning" %}
Make sure to attach messages to ADO recipients as most ADOs do not have a way to withdraw funds meaning sending funds without a message will cause the loss of the sent funds.&#x20;
{% endhint %}

#### JSON Representation of the Message

{% hint style="info" %}
You can use either the JSON representation or the Base64 depending on the Message type you select from the dropdown. In my case I use the Base64.
{% endhint %}

```json
{"send":{}}
```

As Base64:

```
eyJzZW5kIjp7fX0=
```

{% hint style="warning" %}
Here I reference the splitter using "**./splitter-1**" as **splitter-1** is the name of my splitter component in the app.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-04-19 at 4.29.16 PM.png" alt=""><figcaption></figcaption></figure>

After you publish, the sale will start and users can start buying NFTs from the sale. Once the end time is reached, you will need to execute "**End Sale**" twice, once for sending the NFTs, and another time for sending the funds.&#x20;

{% hint style="info" %}
If you want to try buying some of the NFTs from the sale and then checking the results, check our "[Using CLI to Interact with Apps](../using-cli-to-interact-with-apps.md)" section.

If you want to make a custom interface for users to bid on the NFTs, check out our "[Embeddables](../embeddables/)" guide.

If you are interested in using queries on your App components, check out our [Using Queries ](../../learning-the-basics/using-queries.md)section to learn about that process.
{% endhint %}
