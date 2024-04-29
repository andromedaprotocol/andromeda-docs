# Using Queries

&#x20;A query is a way to check or read data from the blockchain without making any updates. Unlike execute messages that modify something in our ADO, queries are just for getting information. We have built many Andromeda Apps in our[ previous guides](../guides-and-examples/ado-builder/). In this section, we will be taking a look at queries and how they work.&#x20;

&#x20;Each ADO type has its own set of unique queries available. For example, let's say someone built an Auction ADO that has some NFTs in auction. As a user, I would want to know what the current highest bid is on said NFT. This information is stored in the ADO and can be retrieved through queries.&#x20;

Each of our ADOs has two sets of queries:

* **Base ADO Queries:** A set of queries common to all our ADOs that provide basic information about the ADO. Examples of base queries include getting the owner, version of the ADO, and many more. The list of base queries can be found [here](https://docs.andromedaprotocol.io/andromeda/platform-and-framework/ado-base/andromedaquery).
* **Custom Queries:** A set of queries unique to each ADO. These queries depend on the use of the ADO     and are specific to that use. Examples of custom queries include getting bids for an NFT in an auction, getting the minted NFTs for a CW721, getting the sale information in a crowdfund etc...

{% hint style="info" %}
Since queries are a read-only operation, they are free of charge, meaning no gas/funds are required to query.&#x20;
{% endhint %}

Similar to execute messages, the queries can be found in the assets page on the panel of the component to be queried:

{% hint style="warning" %}
You can also use queries from our [CLI ](https://docs.andromedaprotocol.io/andromeda/andromeda-cli/introduction)but we will be focusing on the web-app queries in this example.
{% endhint %}

<figure><img src="../.gitbook/assets/Screen Shot 2024-01-22 at 6.18.59 PM 1.png" alt=""><figcaption></figcaption></figure>

When selected, a list of queries will be presented that apply for this ADO (Including both base and cutom queries):



<figure><img src="../.gitbook/assets/Screen Shot 2024-01-22 at 6.21.15 PM.png" alt=""><figcaption></figcaption></figure>

Each query is named in a way that makes it easy to assume what information we are requesting. To get a full explanation of each query, what is needed to call it, and what is returned, refer to the ADO's [documentation page](https://docs.andromedaprotocol.io/andromeda/andromeda-digital-objects/introduction-to-ados) where detailed explanations can be found.

As an example, I will be querying some information from a CW721 component in my Crowdfund App:

{% hint style="info" %}
You can use any CW721 in any App you have built since the queries are the same for the same type of ADO meaning any CW721 will have the same queries not matter the use or app it is part of. If you haven't built any Apps yet, then what are you waiting for, learn how to build your first App [here](../guides-and-examples/ado-builder/building-your-first-app.md).
{% endhint %}

Choose any CW721 component and select the Num Tokens query from the dropdown list:&#x20;

{% hint style="warning" %}
You can access the documentation for the query by clicking on the elipse (Three vertical dots) on the right of the panel and selection documentation.
{% endhint %}

<figure><img src="../.gitbook/assets/Screen Shot 2024-01-22 at 6.53.57 PM.png" alt=""><figcaption></figcaption></figure>

This query requires no fields so you can just press the "Query" button to get the number of tokens minted by the CW721:

<figure><img src="../.gitbook/assets/Screen Shot 2024-01-22 at 6.56.54 PM.png" alt=""><figcaption></figcaption></figure>

Once pressed, the component is queried for the information and the result is returned in JSON format. For my CW721, 1 NFT was minted as seen above.&#x20;

{% hint style="info" %}
You can copy the response data to your clipboard by pressing "Copy Response" if needed.
{% endhint %}

Some queries will require some fields to be specified in order to return the result. Let us query the owner of an NFT. This time we can use the "More Queries" button on the top right instead of going back to the assets page to directly use a new query on the same ADO:&#x20;

<figure><img src="../.gitbook/assets/Screen Shot 2024-01-22 at 6.59.48 PM.png" alt=""><figcaption></figcaption></figure>

Here we wanted to query the owner of a specific NFT so it makes sense that we have to specify which NFT we want to get the owner for by providing the token id. The result is again displayed in the Response section below.&#x20;

This gives you a small introduction on queries and how to use them. The rest is up to you, feel free to play around and test out different queries and their results.&#x20;
