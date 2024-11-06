# Commission Based Sales Application

In this example, we will be looking to set up a scaling commission sales application using the aOS.&#x20;

### Defining our App

{% hint style="info" %}
The App is built on the Andromeda chain.
{% endhint %}

For this build, let us assume the following:

* A company that sells tickets to events wants to use web-3. These tickets are represented as NFTs.&#x20;
* The company employs salesmen that are tasked on getting customers and selling them the tickets.
* The salesmen get a % commision on the total amount of tickets sold each month.
* The commision is not fixed and varies depending on the amount of funds raised by the salesman.&#x20;
* The commission is scheduled to be paid at the end of each month.
* The commission percentages taken by the salesman are the following:

1. 10% if total sales is less than 10 ANDR
2. 15% if total sales is between 10 and 30 ANDR
3. 20% if total sales is between 30 and 100 ANDR
4. 25% if total sales exceecd 100 ANDR

{% hint style="warning" %}
* The product that is being sold can be anything the user wants. In this example we are taking the case of tickets.
* The commission can also be distributed daily, weekly, or whatever the user wants. In this example we using monthly.
* In this example we will focus more on building the workflow of the App rather than the NFT sales themselves. You can check other examples that go through the entire NFT setup process (NFT metadata and embeddable setup)
{% endhint %}

### Defining the Components

To create the App we described above, we need the following components:

* [CW721](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to mint the NFT collection.
* [Marketplace](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/marketplace) component to sell the NFTs.&#x20;
* [Timelock](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/timelock) component to accumulate the funds from sales throughout the month
* [Conditional Splitter](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados/andromeda-digital-objects/conditional-splitter) to apply the commission split based on total amount raised.&#x20;

### Component Workflow

As we have many components in this build, let us describe the workflow for our App to function as intended. These are the steps that we will need to do after App creation:&#x20;

{% hint style="warning" %}
We will be going  through the entire build step by step.
{% endhint %}

1. Mint the NFTs from the CW721.
2. Send the NFTs to the marketplace where they can be sold.
3. Make the Recipient of the marketplace sales the timelock ADO and attach a message telling it to lock the funds for the end of the month.
4. Make the Recipient of the timelocked funds the conditional splitter and attach a message telling it to split the funds sent according to the set conditonal splits .
5. &#x20;At the end of the month, the funds can be released in one go from the timelock to the conditional splitter which will in turn automatically split and sent the commission to the salesman, and the rest of the funds to the company.

## Building the App

Open a new empty project in the ADO Builder and give the app an appropriate name:

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.08.40 PM.png" alt=""><figcaption></figcaption></figure>

**Add a CW721 component and fill in the fields:**&#x20;

{% hint style="info" %}
Use your connected wallet for the **Minter Address** field.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.09.51 PM.png" alt=""><figcaption></figcaption></figure>

**Next, we need to add the Marketplace component:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-30 at 4.09.48 PM.png" alt=""><figcaption></figcaption></figure>

Here we specify native, as we want the sales to be conducted in native uandr tokens. We aslo specify the "**Authorized Token Addresses**" as our CW721 component (by its VFS reference), meaning it is the only CW721 allowed to send NFTs to our marketplace.

**Next, we can add our Timelock component:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.14.44 PM.png" alt=""><figcaption></figcaption></figure>

**Finally, we need to add the Conditional Splitter component:**

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.17.49 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
For multiple salesmen, you would need to add a Conditional Splitter for each one. In this example we will go through the setup for a single salesman.
{% endhint %}

Here we need to configure the Conditional Splitter according to the commissions we have decided in the App definiton. This can be done by defining the **Threshold** field. Select "**Add item**":

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.23.02 PM.png" alt=""><figcaption></figcaption></figure>

There are two main fields needed for each commission tier:

{% hint style="info" %}
You can also add an optional **Lock Time** which makes the recipient list locked (Cannot be changed from the initial declared values by anyone) for the defined time.
{% endhint %}

* **Recipients and Percentages:** The addresses to forward funds to from the conditional splitter. In our case we will have two recipients, the salesman and the company. We will also define the percentage split for each address. &#x20;
* **Minimum Amount:** Defines the minimum amount of funds required for the above **Recipients and Percentages** to be activated. This will be based on the sales thresholds we defined in our [App Definition](commission-based-sales-application.md#defining-our-app).

Based on our commission splits, we need to define 4 **Thresholds** in total. For each, we specify the same addresses as the recipients (one for salesman and one for the company) but change the **Percentage** and **Minimum** **Amount** fields.

Here is what it would look like:

{% hint style="warning" %}
Use your own addresses to represent the salesman and company.

You can zoom in to view the panels in detail.&#x20;

The amounts are specified in uandr (1000000 uandr = 1 ANDR).
{% endhint %}

**Conditional Splitter Panels**

<details>

<summary><strong>First condition: Total > 0</strong></summary>

![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.50.30 PM.png>)![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.50.40 PM.png>)

</details>

<details>

<summary><strong>Second condition: 10  &#x3C; Total &#x3C; 30</strong></summary>

<img src="../../.gitbook/assets/Screenshot 2024-09-04 at 4.55.12 PM.png" alt="" data-size="original">![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.55.23 PM.png>)



</details>

<details>

<summary><strong>Third condition: 30 &#x3C; Total &#x3C; 100</strong></summary>

![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.57.02 PM.png>)![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.57.12 PM.png>)

</details>

<details>

<summary><strong>Fourth condition:  Total > 100</strong></summary>

![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.59.03 PM.png>)![](<../../.gitbook/assets/Screenshot 2024-09-04 at 4.59.11 PM.png>)

</details>



The App is now ready for publishing.

{% hint style="warning" %}
If your App is not publishing you can reference this flex file to make sure the setup is correct
{% endhint %}

{% file src="../../.gitbook/assets/app-Commission Based Sales App-staging.flex" %}

## Setting Up the App

### Mint the NFTs

The first step will be minting the NFTs that represent the tickets. For this example build, 3 NFTs will be minted. We can use the **Batch Mint** message from the CW721 modifier list to mint the 3 NFTs in one go:

{% hint style="warning" %}
We will not setup the NFT metadata in this example, refer to other [guides](building-your-first-nft-collection/setting-up-nft-metadata.md) if you are interested in the process.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 5.14.39 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
Use your connected address as the owner to be able to send the NFTs to marketplace in the next phase.

Make sure to change the Token Id for each NFT as it needs to be unique.
{% endhint %}

Select Add Item and add 2 more NFTs and then publish.

{% file src="../../.gitbook/assets/cw721_2.0.1_batch-mint" %}

### Send NFTs to Marketplace

As we have seen in other  [guides](nft-marketplace-app.md), we need to send the NFTs from the CW721 component to the marketplace component with the desired configuration for the sale.&#x20;

Select **Send NFT** from the list of modifiers for the CW721:

{% hint style="info" %}
You can use our "[Multi Execute](../../learning-the-basics/using-execute-messages.md#multi-execute)" option to send all the NFTs in one go.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 6.00.14 PM.png" alt=""><figcaption></figcaption></figure>

For the **Recipient**, specify the address of the Marketplace ADO in your App. Then, select the Marketplace "**CW721 start sale**" and specify the NFT sale parameters:

{% hint style="warning" %}
You also need to specify the version of the marketplace you are sending to. Make sure to use the same version of your marketplace component. If not sure, you can check the version in the assets page.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 6.00.56 PM.png" alt=""><figcaption></figcaption></figure>

We need the funds from the sales to go to the Timelock component to be locked till the end of the month. To do this, we specify the **Recipient** as the Timelock address.

For the **msg**, we want the Timelock ADO to automatically lock the funds when received. This can be done using the **HoldFunds** execute message for the timelock.&#x20;

Let us take a look on the **HoldFunds** message:

{% hint style="info" %}
You can find more detailed documentation on the Timelock ADO in our [developer docs](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/timelock).
{% endhint %}

```rust
pub enum ExecuteMsg {
    HoldFunds {
        condition: Option<EscrowCondition>,
        recipient: Option<Recipient>,
    },
}
```

There are two fields that need to be specified:

1. **Condition:** The condition that needs to be satisfied in order for the funds to be unlocked. In our case, the condition needs to be reaching the end of the month.
2. **Recipient:** The address to receive the funds from the Timelock when they are claimed after the condition has been satisfied. In our case, we need to send the funds to the Conditional Splitter and specify a **msg** to send the received funds based on the splits we have set up earlier.

Now let us represent this message in JSON so we can add it to the web-app:

{% hint style="warning" %}
If you would like to be able to fully test the App and check the results, it is recommended you use the expiration for the timelock to be in around one hour.&#x20;
{% endhint %}

```json
{
    "hold_funds": {
        "condition": {
            "expiration":{
                "at_time":1727740799000
                }
            },
        "recipient":{
            "address":"andr1n3nq3aqtjnj40fly76sdt0amjh8aa30drzqcqvt8hapvg7nd4jyq42veq6",
            "msg":"eyJzZW5kIjp7fX0="
    }
  }
}
```

The condition part is straight forward. You can use an [epoch converter](https://www.epochconverter.com) to get the timestamp for end of month in milliseconds.

For the recipient, we specify the address of our Conditional Splitter component for the "**address**" field. Here we have to attach a **msg** along with the recipient. This will instruct the Conditional Splitter what to do when it recieves the funds from the Timelock.&#x20;

Since we want to split the funds, we use the "**Send**" execute message from the list of execute messages for a Conditional Splitter. This will instruct it to send the funds directly to the salesman and company (using the split based on total amount) whenever it receives the funds from the timelock. The **msg** is specified as Base64 which is the following for a "**Send**" execute:

**As JSON:**

```
{"send":{}}
```

**As Base64:**

```
eyJzZW5kIjp7fX0=
```

So as a quick recap, we did the following:

1. Tell the marketplace to send the funds from the NFT sales to the Timelock.
2. Tell the Timelock to hold these funds till the end of the month and to go to the Conditional Splitter when claimed.
3. Tell the Conditional Splitter to automatically split the funds it receives from the Timelock based on the configurations we set up at creation.

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-04 at 6.40.56 PM.png" alt=""><figcaption></figcaption></figure>

Once done, we can publish.&#x20;

{% file src="../../.gitbook/assets/cw721_2.0.1_send-nft.flex" %}

{% hint style="warning" %}
The process for the other 2 NFTs will be the same.&#x20;
{% endhint %}

### Next Steps

{% hint style="warning" %}
* If you wish to try out the Application, it would be recommended to build the App and specify the lock time in the Timelock as 1 hour or so.&#x20;
* This setup was for one salesman only. In case of multiple, you would need to have a separate Timelock and Conditional Splitter for each of them.
{% endhint %}

The App has now been successfully set up. You can try buying the NFTs from the marketplace and then claiming these funds from the Timelock using the **Release Funds** modifier and checking the balances for the salesman and the company before and after claiming.&#x20;
