# ADO Ownership

In this section, we will be looking  ADO ownership, what it means, and how it can be transferred from one user to another.&#x20;

### What is ADO Ownership

{% hint style="warning" %}
In case of Apps, the user address creating the App will receive ownership over all the components.
{% endhint %}

Whenever an ADO is created, it registers an owner address inside the ADO's state. By default,  this ownership is assigned to the address that has created the ADO.  This owner address is the only address allowed to call the owner restricted execute messages on the ADO. These messages are usually used to set up the ADO or update it's state. An example would be updating who receives funds from a splitter.&#x20;

### ADO Ownership Transfer

In some cases, a user might want to transfer ownership to another user/address. All our ADOs have this functionality built in, allowing an owner to send a request to another user for ownership transfer. Whenever the request is sent, the second user needs to accept the request for the transfer to occur. This is to prevent users from sending unwanted ADOs to other users without their permission.

Let us go through a small example of tranferring an ADO to another address. In the ADO Builder, create a new App with any component:

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 5.34.51 PM.png" alt=""><figcaption></figcaption></figure>

Now after publishing, go to the assets page, and from the list of execute messages, select "**Ownership**":

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 5.43.53 PM.png" alt=""><figcaption></figcaption></figure>

This will open up the ownership panel:

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 5.56.53 PM.png" alt=""><figcaption></figcaption></figure>

To send a transfership request, select "**Update Onwer**". Here we can specify the following:

* **Expiration:** Specify the time when the request expires. After it is reached, the second user can no longer accept ownership of the ADO.

{% hint style="warning" %}
Ideally, use another one of your Keplr wallets as the new owner. This will allow you to test the full process easily.
{% endhint %}

* **New Owner**: The address you are sending the request to.&#x20;

Once these are specified, you can publish the message. This will take you to the asset view for the component:

{% hint style="warning" %}
You can also access this page through the main assets, by clicking on the eye symbol for the component then selecting "**more info**".
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 6.03.03 PM.png" alt=""><figcaption></figcaption></figure>

In order to complete the transfer, we need to accept the offer using the address that we specified as the new owner. &#x20;

The easiest way to do this, is to share the URL of the asset page of the component. When the second users pastes it into their browser, they will get the following:

{% hint style="warning" %}
You can check out the "Executing on non-owned ADOs" section for more ways to do this.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 8.55.01 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
An easy way to test this would be using 2 of your Keplr wallets and simply switching between them.
{% endhint %}

You can notice a small tag in yellow next to the ADO type that sais "**External ADO**". This signifies that the ADO you are viewing is not owned by your connected address. Now lets change that by accepting the ownership request we have sent to this address.&#x20;

Select "**Modifiers**" and then "**Ownership**" like we did earlier. Then select "**Option 1**" and from the dropdown list, select "**Accept Ownership**":



<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 9.04.54 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
If you are looking to transfer ownership of an entire App, you would need to do this on every component, including the App component itself.

You can also find a "**Revoke ownership offer**" message. This can be called by the original owner to remove the offer for the ADO transfer. Can only be done before the new owner has accepted the offer.
{% endhint %}

Once published, the ADO ownership transfer is complete and this address is now the new owner of the ADO. Looking at the assets page, we can see that the timelock is now in the new owner's assets:

{% hint style="warning" %}
As we did not transfer the App, make sure the asset filter is not set to Apps as that is the default for it. Otherwise the ADO will not show.
{% endhint %}

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 9.08.15 PM.png" alt=""><figcaption></figcaption></figure>

### Disown an ADO

Another option that users have, is to disown an ADO. To disown an ADO, means to relinquish ownership from that ADO forever, making it impossible for anyone to ever own it again.&#x20;

This can be used in cases where the owner of an application wants to ensure the users that the configurations of the ADO will never be changed ever again. Whether that be the % royalty on NFTs or a fee taken by the ADO, once the ADO is disowned, these will remain the same forever.

{% hint style="warning" %}
Disowning an ADO does not mean that it will no longer function, but all owner restricted messages can never be called ever again.
{% endhint %}

To disown an ADO, we also use the "**Ownership**" execute message and select "**Option 1**" and then the "**Disown**" option from the dropdown:

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 9.34.31 PM.png" alt=""><figcaption></figcaption></figure>

Once published, if you select the eye symbol for the ADO from the assets page, you will notice the owner address is now "null":

<figure><img src="../.gitbook/assets/Screenshot 2024-10-08 at 9.38.22 PM.png" alt=""><figcaption></figcaption></figure>

