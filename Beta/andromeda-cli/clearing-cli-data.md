# Clearing CLI Data

The CLI stores data such as your chain configs and wallets/keys on your device. This data is saved in a hidden folder usually located in your home directory (\~). Uninstalling the CLI will not clear this data. You will have to delete the files yourself. In order to do so, go to your home directory and locate the **.andr-cli** folder and delete it.&#x20;

{% hint style="warning" %}
The keys stored are encrypted.
{% endhint %}

The easiest way to do so is throught a terminal. Run:

```
cd ~
rm -rf .andr-cli
```

This will delete the **.andr-cli** folder and all the stored CLI data.&#x20;

Alternatively, you can locate this folder yourself in your home directory and simply delete it.&#x20;

{% hint style="warning" %}
Keep in mind that the folder will be a hidden folder. You will need to check how to make the folder visible on your machine.&#x20;
{% endhint %}
