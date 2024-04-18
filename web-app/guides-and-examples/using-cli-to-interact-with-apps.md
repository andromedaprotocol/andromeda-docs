# Using CLI to Interact with Apps

In this section, we will be taking a look on how we can use the Andromeda CLI to interact with the apps we have built. We use the CLI since the web-app only allows execution of messages on currently owned ADOs. This means you can only interact with an ADO if you are the owner.&#x20;

In most cases, the owner of an ADO is not allowed to bid/buy anything they put up for sale, so we need to interact with these apps using different addresses representing buyers/users wanting to interact with your apps.

{% hint style="info" %}
We have a full section in our docs covering the CLI and it's functionality. If interested in learning more about it then check out the [Andromeda CLI ](https://docs.andromedaprotocol.io/andromeda/andromeda-cli/introduction)docs.

Make sure to build some apps using our [ADO Builder ](ado-builder/)examples before jumping into this section.
{% endhint %}

The first thing you will need to do is download the CLI and create a new address.

## Installation and Set Up

### Step 1: Install NPM

We will download the Andromeda CLI through npm. To check if you have npm already installed, run:

```
npm -v 
```

If the npm version appears,then you are good to go. If not, you can easily download it from [here](https://nodejs.org/en/download/).

### Step 2: Download the Andromeda CLI

To download the Andromeda CLI run the following command in your terminal:&#x20;

```
sudo npm i -g @andromedaprotocol/cli
```

Then run "andr" to open the CLI:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.09.31 PM.png" alt=""><figcaption></figcaption></figure>

### Step 3: Connect to the Desired Chain&#x20;

To connect to a chain run:

{% hint style="warning" %}
Connect to the chain that you have built your apps on.
{% endhint %}

```
chain use 
```

Select the testnet you want to connect to.

### Step 4: Create a New Wallet

{% hint style="info" %}
If you already have a wallet that needs to be imported to the CLI, go to "[Recovering a Wallet](using-cli-to-interact-with-apps.md#recovering-a-wallet)" section.
{% endhint %}

To create a new wallet run:&#x20;

```
wallet add
```

You will be prompted to select a name and password for the wallet. Once selected, you will get your seed phrase which is compromised of 24 words. This seed phrase is the only way an address can be recovered so make sure to keep it somewhere safely.

{% hint style="danger" %}
The 'private key'/'seed phrase' is always shown once. Please make sure to write it down and store it somewhere safe.
{% endhint %}

After saving your seed phrase, you will be asked to input some of the words.&#x20;

Finally run:

```
wallet list
```

The wallet you created should appear with the name and address.

### Recovering a Wallet&#x20;

If you forget your password or lose your key, or you would like to use your key in another place, you can recover your key by using the `--recover` flag.

The following example will recover a key with the seed phrase and store it in the local keystore with the name test:

```
wallet add test --recover
```

You'll be asked to enter and repeat the new password for your key, and enter the seed phrase. Then you get your key back.

## Interacting with Andromeda Apps

Now that you have a wallet set up, you can start executing some messages on your apps.&#x20;

### Buying NFTs from an Auction

If you have built our [ADO Builder](ado-builder/) examples, then you already know how to setup a custom auction for  NFTs. In the examples we covered, we always stopped after the auction set up. Now, we will be buying some NFTs as a user and checking that everything we set up worked as intended.&#x20;

Before we continue, you will need to build an Auction app to use. You can follow the "[Creating your First NFT Auction House](ado-builder/creating-your-first-nft-auction-house.md)".&#x20;

{% hint style="info" %}
If you have already built the app, you don't have to do it again.
{% endhint %}

In order to bid on an NFT in an auction, a sale on the NFT must be ongoing. In the last steps of our example, we started an auction sale on an NFT specifying the start time, duration, and other properties. Make sure that there is an ongoing auction on the NFT before attempting to bid.

{% hint style="info" %}
If the auction you started when following the example has expired, then execute the **Claim** execute message of the auction on the NFT. Once claimed, you can start a new auction on the NFT. Alternatively, you can mint a new NFT and start an auction on it.&#x20;
{% endhint %}

First thing we need to do is get the contract address of the ADO we want to execute on. In our case, we want to bid on an NFT meaning we need to execute on the **Auction** component in the app. You can get the address of the Auction component from the assets page:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.24.21 PM.png" alt=""><figcaption></figcaption></figure>

Now in the CLI, we run:

```
ado execute <auction-contract-address>
```

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.31.46 PM.png" alt=""><figcaption></figcaption></figure>

This will get the list of execute messges for an auction for you to choose from. Since we want to place a bid on an NFT, select **Place Bid.** Once selected, you will be prompted to fill in the messages fields. For placing a bid, you have to specify the following:

* The contract address of the CW721 ADO used. We get this from the assets page.
* Token Id of the NFT to bid on.
* The amount of funds to attach. This will be the amount of the bid.

In the image below, I place a 1000 uandr bid on the NFT with token Id 1:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.41.22 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Feel free to create many wallets and bid multiple times on the same NFT.
{% endhint %}

Now we wait for the auction to end. In the meantime, let us check some balances to make sure that the funds were allocated correctly when we claim the NFT later on.&#x20;

To check the balance of an address we can run:

```
bank balance uandr <address-to-check>
```

Let us check the balance of the seller (Connected wallet on the web-app):

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.51.57 PM.png" alt=""><figcaption></figcaption></figure>

When the expiry of the auction is reached, claim the NFT:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.55.16 PM.png" alt=""><figcaption></figcaption></figure>

Let's get the balance of the seller once more:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 4.57.44 PM.png" alt=""><figcaption></figcaption></figure>

As you can see, the balance of the seller has increased by 1000uandr exactly after the NFT was claimed. Now let us check the owner of the NFT that was sold. We can do that by performing a query on the **CW721** ADO in our app.&#x20;

There are two main types of messages for an ADO after it has been published:

1. **Execute Messages**: Used to perform some action on an ADO. We have already seen how these messages work.
2. **Query Messages**: Used to fetch information on an ADO. Queries do not cost any fees. &#x20;

{% hint style="warning" %}
The list of execute and query messges available for each ADO can be found in the ADO's own documentation page.&#x20;
{% endhint %}

To run a query on an ADO, we run the following in the CLI:

```
ado query <contract-address-of-ADO-to-query>
```

I want to check the owner of the NFT I sold to make sure that it was transferred to the buyer:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-23 at 5.11.56 PM.png" alt=""><figcaption></figcaption></figure>

The owner is the same as the buyer, so I can be sure that the app has acted as expected.&#x20;

### Interacting with Other Apps&#x20;

&#x20;You can perform similar steps to interact with any apps you build. Here is a list of what you can do for the other apps:

#### Auction with Royalties

1. Bid on the NFTs
2. Check the change in balances of the buyer, seller, and recipients of the royalties
3. Check using a query the new owner of the NFT

#### Marketplace with Taxes

1. Buy NFTs&#x20;
2. Check the change in balances of the buyer, seller, and recipients of the taxes
3. Check using a query the new owner of the NFT

#### Crowdfund

1. Buy NFTs from the crowdfund
2. Check the change in balances of the buyer, seller
3. Check the balances of the addresses that received the funds from the Crowdfund
