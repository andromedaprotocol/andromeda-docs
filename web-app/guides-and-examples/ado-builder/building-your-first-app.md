# Building Your First App

In this guide, you will be utilizing the ADO Builder tool to construct custom Andromeda Apps. To begin, launch the ADO Builder and select the option for an Empty Project. This option should be used when starting from scratch and building an app from the ground up:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.04.53 PM 1.png" alt=""><figcaption><p>Selecting a Blank Canvas</p></figcaption></figure>

{% hint style="warning" %}
Note: The app will utilize the Stargaze chain, as indicated by the connected wallet on the top right corner of the screen. Feel free to use any of the chains available as the steps of building the App will remain the same. If you do not know how to connect your wallet to the web-app, check out our “[Setting up your walle](../../learning-the-basics/setting-up-your-wallet.md)t” guide.
{% endhint %}

Upon selecting the **Empty Project** option in the ADO Builder, you will be taken to the main ADO builder interface:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.07.22 PM.png" alt=""><figcaption></figcaption></figure>

As you can see, the empty project starts with the base component which is the **App** component. The **App** component has a single field for you to specify the name of your application. For the first app, you will not add any components. Give your app a name and we can proceed to deploying it on chain.&#x20;

To deploy the app, simply press the “**Publish**” button. It is important to note that the app will run on the chain you are currently connected to, so make sure you are connected to the desired chain before proceeding with publishing:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.09.46 PM.png" alt=""><figcaption></figcaption></figure>

Before the app gets deployed on the chain, you are given an estimate of the costs required to do so. In my case above, the estimated cost is 12880 **ustars** or 0.012880 **stars**. If I multiply this number with the price of **stars**, I can estimate the dollar cost of publishing my app. If satisfied with the result, press “**Broadcast**” to go through with the deployment. Lastly, you will get the following screen:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.12.38 PM.png" alt="" width="358"><figcaption></figcaption></figure>

At the top, we have the "**Messages"** that show the generated message of your app that will be broadcast to the chain.

In addition to the message, you also have the option to add a memo or note to the transaction. This can be used to provide additional information about the app or to include any relevant details about the transaction.

Select “**Approve**” to give the final approval for broadcasting the app to chain:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.14.51 PM.png" alt="" width="375"><figcaption></figcaption></figure>

Congratulations, you have successfully deployed your first Andromeda App on chain. Pressing on the transaction hash will open up the transaction in a block explorer where all the details of the transaction are displayed:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.17.09 PM.png" alt=""><figcaption></figcaption></figure>

Back to the ADO builder. When you press on “**Close**”, you are taken to the Assets page:

{% hint style="info" %}
The Assets page is where you can view all the Apps/ADOs that have your connected wallet as the owner. By default any App/ADO you build will have the connected address as the owner.&#x20;
{% endhint %}

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.19.33 PM.png" alt=""><figcaption></figcaption></figure>

For each component of the app (Including the App component itself), you are given some key information as seen above:

* **Name**: The assigned name of the component
* **Type**: The type of the component/ADO along with the version
* **Block** **Height**: The block height at which the App was created
* **Address**: The contract address of the component

Additional information can be displayed by pressing on the view button (Eye symbol) to the right of the Address.

When you want to communicate with an app, you can do so by "executing messages." To execute a message, simply find the component you wish to communicate with and click on the three dots to the right of it. This will bring up a list of all the messages that can be executed by that particular component. Here we only have one component, which is the App component itself:

<figure><img src="../../.gitbook/assets/Screen Shot 2024-03-14 at 5.31.05 PM.png" alt=""><figcaption></figcaption></figure>

For now, you will not execute any messages as the App is empty and basically useless.&#x20;

{% hint style="warning" %}
You can read about each ADO's execute messages in the ADO's documentation page.&#x20;
{% endhint %}

If you feel confident in what we just learned, jump in the next guide [Building your First NFT Collection ](building-your-first-nft-collection/)where we take things one step further.
