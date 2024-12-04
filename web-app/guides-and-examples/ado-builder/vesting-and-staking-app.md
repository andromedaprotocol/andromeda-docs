# Vesting and Staking App

In this example, we will be building a simple yet effective vesting application.

### Defining our App

{% hint style="warning" %}
The App is built on the Andromeda Chain.

The build is done on testnet using the Beta ADOs. However, these ADOs may be outside of beta by the time you’re reading this.
{% endhint %}

For this build, let us assume the following:

* A web-3 project is launching their own native token.
* Employees of this company get an certain amount of tokens that will be vested for 1 year.
* When the vested tokens are claimed, they are automatically staked.
* The company will be building the Apps for its employees.

### Defining the Components

[Vesting ](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados-1/andromeda-digital-objects/vesting)component to vest the native tokens.

[Staking](https://docs.andromedaprotocol.io/andromeda/andromeda-beta-ados-1/andromeda-digital-objects/staking) component to stake the tokens once released from vesting.

## Building the App

{% hint style="warning" %}
We will be creating the App for one employee. Each employee would need a separate App for them which can be built the same way we are doing this one.&#x20;
{% endhint %}

Open a new empty project in the ADO Builder and give the app an appropriate name:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-12 at 12.13.29 PM.png" alt=""><figcaption></figcaption></figure>

### Vesting Component

First component we will add is the Vesting ADO:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-13 at 10.54.32 AM.png" alt=""><figcaption></figcaption></figure>

Here we have to specify two fields:&#x20;

1. **Denom**: The micro denomination of the token that will be staked. In our case, we will be using the Andromeda chain/token, so we can specify "**uandr**".
2. **The Recipient**: The address or ADO who will receive the tokens once vesting is complete. In our case, we want the tokens to go to the staking ADO whenever they are claimed from vesting, so we need to reference it. We can do that by using "./" followed by the component name for the staking ADO.

{% hint style="warning" %}
By default, the staking ADO will have the name "validator-staking-1". If you decide to change this default name, make sure you use the correct reference here.
{% endhint %}

So far, we have instructed the vesting ADO to release the vested tokens to the staking ADO. When the these tokens are sent, we want them to be automatically staked. To do so, we will have to attach a message along with the recipient.&#x20;

To do this, we select "**Option 1**" for the message and we specify the staking message of the Staking ADO. From the docs we can see that the message is the following:&#x20;

```rust
pub enum ExecuteMsg {
    Stake {
        validator: Option<Addr>,
    },
}
```

The message needs to be attached as base64. The web-app supports using JSON format that will be auto converted for us. Select JSON instead of base64 and paste the below JSON message into the text box:

{% hint style="info" %}
You can find validator addresses in our [testnet explorer](https://explorer.testnet.andromedaprotocol.io/galileo-4/staking).
{% endhint %}

```json
{"stake":{"validator":"andrvaloper1rrymqwre6c9m5cqltjnfpahqaj3t5qw2prqqtv"}}
```

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-13 at 11.16.56 AM.png" alt=""><figcaption></figcaption></figure>

The vesting component is now configured and we can move on to the staking component.

### Staking Component

Add the Validator Staking ADO.

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-13 at 11.20.37 AM.png" alt=""><figcaption></figcaption></figure>

The default validator address defines the address to be used in case we call a staking message without a specified validator. We can use the same validator address used in our attached message.

{% hint style="info" %}
We could have opted to not specify the validator in our attached message earlier as the staking ADO will use the default validator which is the same in our case.&#x20;
{% endhint %}

Now the App is ready, go ahead and publish.&#x20;

## Setting Up the App

### Creating the Vesting Batch

First thing we will do, is create the vesting batch for the employee which contains the amount of tokens to be vested and the vesting schedule to use.

Select the "**Create Batch**" message for the Vesting component:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-14 at 9.16.29 AM (1).png" alt=""><figcaption></figcaption></figure>

This will open up the a panel where we can configure the vesting schedules:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-14 at 10.04.12 AM.png" alt=""><figcaption></figcaption></figure>

Here we specified the following:

1. **Lockup Duration:** Amount of time where the tokens are completely locked and vesting is yet to start.&#x20;
2. **Release Amount:** Amount of tokens to be released every release interval. We can use a fixed amount of tokens, or a percentage of the total amount. In this example, 5% is specified.
3. **Release Interval:** Amount of time between every release. Releases start after the lockup duration has ended. 10 min is specified here.

This means that once we publish this message, the tokens will be locked for 1 hour. Then every 10 min, 5% of the tokens will be released and can be claimed by the contract owner.

Before we publish, we need to provide the tokens that will be vested. To do so, we attach funds to the message by selecting "**Add Attachment**", then "**Add Fund**":

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-14 at 10.08.06 AM.png" alt=""><figcaption></figcaption></figure>

Here we attach 10 ANDR tokens. Now we can publish.

### Transferring Ownership&#x20;

The App is now set up. After 1 hour, the tokens will start to vest and will be released every 10 min to claim. When claimed, they will go to the defined recipient, who in our case is the **Staking** ADO and will be automatically staked for us.&#x20;

Now as the company, we have successfully created the App. Both the **Vesting** and **Staking** ADOs (You can read more in docs) are set up in a way where the contract owner has all the controll over them. For example, only the contract owner is able claim tokens from the **Vesting** ADO, and any claimed rewards/unstaked tokens from the **Staking** ADO will go to the contract owner. This means that we need to transfer ownership of this App, or at least the components, to the employee we have set this up for.&#x20;

You can find the steps for ownership transfer in our [ADO Ownership](../../learning-the-basics/ado-ownership.md) section.

### Checking the Results

Once the lockup period has passed, and one release interval has been reached, we can claim some tokens from vesting and check the status of our staking ADO.

{% hint style="warning" %}
If you have transferred ownership, you will need to perform these messagses using the new owner.
{% endhint %}

From the list of modifiers for the vesting component, select the **"Claim All"**  message. This will allow you to claim any tokens that have finished vesting:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-21 at 2.01.29 PM.png" alt=""><figcaption></figcaption></figure>

You dont need to specify anything here and can just publish. Next, let us check if the tokens have been staked. From the list of queries for the staking ADO, select "**Staked Tokens**":

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-21 at 3.19.02 PM.png" alt=""><figcaption></figcaption></figure>

Then lets query our staked tokens:

{% hint style="warning" %}
By not specifying the Validator Address, the default validator will be taken.
{% endhint %}

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-21 at 3.21.24 PM.png" alt=""><figcaption></figcaption></figure>

As we can see, there are **500000uandr** tokens staked which is equal to **0.5** ANDR, 5% of the 10 ANDR vested. Since we just claimed one release from the vesting, and each release is 5%, then the staked amount is correct. Now every time new tokens are done vesting, we can claim them and have them be automatically staked as we have seen.

Finally, we can claim the rewards from staking by simply calling the "**Claim**" message from the staking components:

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-21 at 3.25.01 PM.png" alt=""><figcaption></figcaption></figure>

Once published, any rewards from staking will be sent to your address.
