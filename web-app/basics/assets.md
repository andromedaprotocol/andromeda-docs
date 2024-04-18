# Assets

### Introduction <a href="#intro-0" id="intro-0"></a>

The **Assets** section in the Andromeda web-app contains all of the owned ADOs and Apps of the connected user (Connected address from Keplr). It is the primary place that is used to interact and modify these Apps and ADOs.

To get to the asset explorer, you need to choose it from the navigation bar on the left-hand side of the Andromeda web-app:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 5.38.18 PM.png" alt="" width="239"><figcaption></figcaption></figure>

Your asset page should open and will look something like this:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 5.39.40 PM.png" alt=""><figcaption></figcaption></figure>

You might notice that there are no assets listed here. This is because only assets that belong to the connected chain/address will show. Since I have not built any ADOs on this chain, my assets list is empty. Now, let me connect to another chain that I have previously built Andromeda Apps on. This is possible by clicking the address on the top right of the screen and then choosing Switch and picking the chain I want to connect to:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 5.48.23 PM.png" alt="" width="320"><figcaption></figcaption></figure>

Here I have chosen to connect to the Andromeda test-net knowing that I have previously built some assets on it:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 5.55.00 PM.png" alt=""><figcaption></figcaption></figure>

As you can see, the assets have appeared. For each asset, the following information is showcased:

* The name of the asset
* The type of asset including the version
* The block height that the asset was published
* The contract address of the ADO

You can get the same information for each ADO in the App by clicking on View:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 5.59.16 PM.png" alt=""><figcaption></figcaption></figure>

## Interacting with Assets <a href="#interacting-20with-20assets-1" id="interacting-20with-20assets-1"></a>

As discussed before, the assets page is used to interact with the Apps/ADOs/NFTs of a user. This is done by clicking on the three dots (ellipses) of the components we want to interact with:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-29 at 6.02.42 PM.png" alt=""><figcaption></figcaption></figure>

When pressed, the list of actions/messages that can be performed on the component will be shown. You can then choose the one you want to perform/execute.

The chosen action/message will load the appropriate panels/components to be filled in by you. Once completed, you can publish the action/message which will execute it on chain. We will learn more about interacting with assets in the [Guides and Examples](broken-reference) sections.

### Sorting and Filtering

A user can have hundreds or even thousands of Apps built on the web-app. This might make it difficult to locate a certain application or component in the midst of all the assets. For this reason, we have added the options to sort and filter your assets based on their type, date of creation, or even name:

<figure><img src="../.gitbook/assets/Screen Shot 2024-04-16 at 5.28.48 PM.png" alt=""><figcaption></figcaption></figure>

Here you have the following options:

* Type the name of the component or App you are searching for in the search bar.
* Select the type of ADO you want to appear in assets (App Contract in screenshot above).
* Choose how to sort the ADOs, either by descending order (newest to oldest) or ascending order (oldest to newest). If not specified, then the ADOs will appear in descending order.
