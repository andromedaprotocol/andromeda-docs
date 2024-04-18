# Importing/Exporting Data

{% hint style="info" %}
The examples here are performed using the App Builder. The same methods can be used using the ADO builder. The export buttons are found next to the publish button in the ADO builder.&#x20;
{% endhint %}

There are several options to export a build:

* [Exporting to CLI](importing-exporting-data.md#exporting-to-cli)
* [Downloading to device](importing-exporting-data.md#downloading-to-device)
* [Exporting using a URL](importing-exporting-data.md#exporting-using-url)&#x20;

### Exporting to CLI

{% hint style="info" %}
If unfimiliar with the Andromeda CLI, you can read about it [here](https://docs.andromedaprotocol.io/andromeda/andromeda-cli/introduction).&#x20;
{% endhint %}

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 5.54.13 PM.png" alt=""><figcaption><p>Copy CLI Command Button</p></figcaption></figure>

Builds/Panels can be exported to the [Andromeda CLI](https://docs.andromedaprotocol.io/andromeda/andromeda-cli/introduction) to be published to the chain. By selecting the "Copy CLI Command" button, a CLI command of what you have built will be created. You can then paste it in the CLI and run it.&#x20;

{% hint style="info" %}
This works with execute messages as well.&#x20;
{% endhint %}

Let us go through a small example to demonstrate this. Import a template and fill it with any data:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-24 at 4.15.09 PM.png" alt=""><figcaption></figcaption></figure>

Now, press the "Copy CLI Command" and paste in the CLI and run. The CLI will simulate the transaction with an approximate Fee cost and will prompt if you want to proceed with the transaction:

<figure><img src="../.gitbook/assets/Screen Shot 2023-05-24 at 4.31.22 PM.png" alt=""><figcaption></figcaption></figure>

Once you accept, the transaction is broadcast to the chain and the app that was created with all the data and information will be on chain.&#x20;

### Downloading to Device

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 5.56.14 PM.png" alt=""><figcaption><p>Save Button</p></figcaption></figure>

Using the **Export** button, you can save a build to your device. When pressed, a .flex file will be downloaded. This file will contain whatever panels/configurations/Apps you had on the canvas. The file can be shared with others and loaded to the web-app on a later date.

Let us go through a quick example on exporting a build. Select a template and fill in the fields with any data:

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 6.03.19 PM.png" alt=""><figcaption><p>Example Build to Export</p></figcaption></figure>

Now save the build by pressing the Export button and giving the file a suitable name:

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 6.12.46 PM.png" alt=""><figcaption><p>Naming the .flex File</p></figcaption></figure>

Open a new blank canvas and select the **Load** button on the top left of the screen:

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 6.17.04 PM.png" alt=""><figcaption><p>Loading a .flex File</p></figcaption></figure>

Once selected, you will need to select the file to load in. Select the .flex file you just downloaded and voila, you are back where you left of.&#x20;

### Exporting Using URL&#x20;

<figure><img src="../.gitbook/assets/Screen Shot 2023-04-26 at 6.26.25 PM.png" alt=""><figcaption><p>Copy Flex URL Button </p></figcaption></figure>

The last method for exporting a build is using a URL. This method is the fastest way to share a build with someone. Just select the **Copy Flex URL** button and share it. The receiving party can just paste it in their search engine opening a canvas of the build.
