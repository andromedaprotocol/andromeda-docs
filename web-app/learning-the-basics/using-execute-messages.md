# Using Execute Messages

## What are Execute Messages?

Execute messages are transactions sent to a smart contract, instructing it to perform specific actions or operations. These messages can trigger functions defined within the smart contract code, enabling users to interact with the contract's functionality. In short, execute messages are the messages we use to interact with an ADO.&#x20;

Each of our ADOs has two sets of execute messages:

* **Base ADO Execute:** A set of execute messages common to all our ADOs that provide basic information about the ADO. Examples of base executes include transfering ownership of an ADO or setting permissions for an ADO. The list of base executes can be found [here](https://docs.andromedaprotocol.io/andromeda/platform-and-framework/ado-base/andromedamsg).
* **Custom Execute messages:** A set of execute messages unique to each ADO. These execute depends on the use of the ADO  and are specific to that use. Examples of custom executes include minting an NFT in an CW721, bidding on an NFT in an Auction, sending some tokens in a CW20.

The execute messages can be found in the ADO's panel in the assets page by pressing on the three dots as seen in the image below:

{% hint style="info" %}
You can also use execute messages from our [CLI ](https://docs.andromedaprotocol.io/andromeda/andromeda-cli/introduction)but we will be focusing on executing from the web-app in this example.
{% endhint %}

<figure><img src="../.gitbook/assets/Screen Shot 2024-04-25 at 6.25.45 PM (1).png" alt=""><figcaption></figcaption></figure>

This will display the list of execute messages available for that component. Each execute will perform a different action on the ADO. You can read about each ADO's execute messages [here](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/introduction-to-ados). Selecting the execute message that you want to perform, will take you to a new page where you can set up the message.&#x20;

### Example

{% hint style="warning" %}
If you dont have any assets yet, it is fine to just read and understand how an execute message works for now. You will get plenty of hands on examples in our [ADO Builder](../guides-and-examples/ado-builder/) guides.
{% endhint %}

Let us perform an execute message to mint some NFTs. On any CW721 ADO I will select the three dots and choose the **Mint** execute message from the list:

<figure><img src="../.gitbook/assets/Screen Shot 2024-04-25 at 6.32.45 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screen Shot 2024-04-25 at 7.26.35 PM (1).png" alt=""><figcaption></figcaption></figure>

Here, you can set up the minting options to mint an NFT:

<figure><img src="../.gitbook/assets/Screen Shot 2024-04-25 at 7.28.55 PM.png" alt=""><figcaption></figcaption></figure>

Once you have configured your execute message, you can press the publish button to send the message to the chain and have the ADO execute the mint. Once published, your CW721 ADO will have 1 minted NFT. You can check this by performing a query which we will cover in the next section.

### Multi Execute

To make life easier, we have created a multi-execute option for our users which allows them to send multiple execute messages in one go:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-01 at 8.20.41 PM.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-01 at 8.35.32 PM.png" alt=""><figcaption></figcaption></figure>

Selecting "**Add Attachment**" will allow us to attach a new execute message. For the first message, I want to burn the NFT I minted earlier so I select the Burn message from the options:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-01 at 8.37.47 PM.png" alt=""><figcaption></figcaption></figure>

Next I want to mint a new NFT. So I select **Add Attachment** once more and select **Mint** from the list of execute messages:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-01 at 8.39.40 PM.png" alt=""><figcaption></figcaption></figure>

I can keep adding messages as I see fit. When done, I can publish all these messages to the chain in one go.

### Execute Message Attachments

In some cases, an execute message will require funds to be sent along with the message. For example, let us say you are buying an NFT on the **Andromeda** chain from a marketplace for 10000uandr, you cant just execute the **Buy**  message without sending any funds along with it to actually pay for the NFT. To do so, you select the Add Attachment:

<figure><img src="../.gitbook/assets/Screenshot 2024-08-19 at 6.55.03 PM.png" alt=""><figcaption></figcaption></figure>

Then select Add Fund:

<figure><img src="../.gitbook/assets/Screenshot 2024-08-19 at 6.56.46 PM.png" alt=""><figcaption></figcaption></figure>

Once selected, the following panel will show up:

<figure><img src="../.gitbook/assets/Screenshot 2024-08-19 at 6.58.19 PM.png" alt=""><figcaption></figcaption></figure>

Here you can sepcify:

* **Coin Denom:** The micro-denomination of the funds you are sending. This is usually the native funds used by the chain you are connected to. In the example we mentioned above we are on the **Andromeda** chain, so it would be "uandr".
* **Amount:** The amount of funds to send which should be the price of the NFT in the case of a marketplace.&#x20;

{% hint style="warning" %}
Funds attachments are also used to pay off any fees that might be assosiated with an execute message.
{% endhint %}
