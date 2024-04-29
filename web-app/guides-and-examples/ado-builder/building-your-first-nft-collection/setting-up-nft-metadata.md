# Setting Up NFT Metadata

## Introduction

One essential aspect of creating a proper NFT collection is defining its metadata. Metadata provides crucial information about an NFT, including its name, description, image, and other relevant attributes. This data needs to be hosted somewhere as the metadata is not stored on-chain. In this guide, we will be taking a look at the steps required to properly set up metadata for an NFT.

### Defining the Metadata

Metadata is data related to the NFT, that provides all the information about the NFT. For Andromeda NFTs, we have decided to follow the [**OpenSea standards for NFT metadata**](https://docs.opensea.io/docs/metadata-standards)**.** The properties that can be included for the NFT are the following:

{% hint style="warning" %}
This does not mean that the user cannot have any additional properties, but the standard contains the properties that would integrate with most NFT marketplaces including our [embeddables](../../embeddables/).
{% endhint %}

| Property           | Description                                                                                                                                   |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `image`            | This is the URL to the image of the item. Can be just about any type of image. We recommend using a minimum 3000 x 3000 image.                |
| `image_data`       | Raw SVG image data, if you want to generate images on the fly (not recommended). Only use this if you're not including the `image` parameter. |
| `external_url`     | A URL to the site of the project.                                                                                                             |
| `description`      | A human-readable description of the item. Markdown is supported.                                                                              |
| `name`             | Name of the item.                                                                                                                             |
| `attributes`       | These are the attributes for the item, which will show up on the page for the item (In Embeddables).                                          |
| `background_color` | Background color of the item on OpenSea. Must be a six-character hexadecimal _without_ a pre-pended #.                                        |
| `animation_url`    | A URL to a multi-media attachment for the item.                                                                                               |
| `youtube_url`      | A URL to a YouTube video (only used if animation\_url is not provided).                                                                       |



### Preparing the Data

NFT metadata should be stored in JSON format and hosted online so it can be linked by a URL (Token Identifier). In this example, I will be using [pinata](https://www.pinata.cloud) to host the NFT metadata and image as it is easy to use. You can opt to use any hosting software such as ipfs directly or others. The metadata JSON schema will look something like this:

{% hint style="info" %}
You can use this as a template.&#x20;

Here `trait_type` is the name of the trait, `value` is the value of the trait, and `display_type` is a field indicating how you would like it to be displayed. If `display_type` is ommited, then it will default to the `trait_type`.
{% endhint %}

```json
{
  "name": "",
  "description": "",
  "image": "",
  "attributes": [
    {
      "display_type":"",
      "trait_type": "",
      "value": ""
    },
    {
      "trait_type": "",
      "value": ""
    }
  ]
}
```

### Example Use Case

To make things more interesting, let us create a use case for our NFT. Let us assume an organizer is looking to sell tickets for their blockchain event using NFTs. Each NFT represents a ticket that a user can buy to get access for the event.&#x20;

{% hint style="info" %}
We will set up the data for one type of ticket in this example. The logic for other NFTs will be the same.
{% endhint %}

#### NFT Image

Each NFT should have an image to showcase the NFT. For this example, I will be using the follwing image:

<figure><img src="../../../.gitbook/assets/ticket.jpeg" alt=""><figcaption></figcaption></figure>

#### Sign in Into Pinata Account

Create a Pinata account and sign in. You should be taken to the following page:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.27.33 PM.png" alt=""><figcaption></figcaption></figure>

#### Host the Image

First step will be hosting our image online so we can reference it in our metadata image property. Select "**choose a file"**, and choose the image you want to host:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.31.13 PM.png" alt=""><figcaption></figcaption></figure>

The image will now appear as seen above.&#x20;

#### Create the Metadata File

As we have mentioned earlier, the metadata is stored in JSON in a JSON file. Open any editor and create a .json file and paste in the JSON template.

{% hint style="info" %}
I will use VS code as my editor.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.38.45 PM.png" alt=""><figcaption></figcaption></figure>

Now we can start filling in the metadata. First let us insert the image URL which we just hosted on pinata. For each file hosted, you will get a CID for the file which is to the right of my uploaded file:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.42.42 PM.png" alt=""><figcaption></figcaption></figure>

Then you can insert this CID into "https://gateway.pinata.cloud/ipfs/{CID}" to get the full URL. In my case the URL is [https://gateway.pinata.cloud/ipfs/QmXvG2oiJHoBRncfEiBtdnNLyYFbAwYRcHUWuGW9kmMmM5 ](https://gateway.pinata.cloud/ipfs/QmXvG2oiJHoBRncfEiBtdnNLyYFbAwYRcHUWuGW9kmMmM5)

Now we insert this as the URL for our image. This image will be linked to the NFT and is displayed in the marketplace or embeddable of the NFT. For the rest of the properties, we can fill them in with whatever makes sense for the NFT:

{% hint style="info" %}
Feel free to add any of the other properties we saw earlier.
{% endhint %}

```json
{
  "name": "DAY 1 - Blockchain Beginnings",
  "description": "Experience the first day of the Blockchain Convention, focusing on the fundamentals and origins of blockchain technology.",
  "image":"https://gateway.pinata.cloud/ipfs/QmXvG2oiJHoBRncfEiBtdnNLyYFbAwYRcHUWuGW9kmMmM5", 
  "attributes": [
    {
      "trait_type": "Day",
      "value": "Day 1"
    },
    {
      "trait_type": "Theme",
      "value": "Blockchain Beginnings"
    },
    {
      "trait_type": "Access Level",
      "value": "General"
    },
    {
      "trait_type": "Event",
      "value": "Blockchain Convention"
    },
    {
      "trait_type": "Benefits",
      "value": "Access to Day 1 Sessions, Complimentary Program"
    },
    {
      "trait_type": "Validity",
      "value": "Day 1 Only"
    }
  ]
}
```

Taking the above and pasting it into my .json file I created earlier, will complete my metadata file:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.51.13 PM.png" alt=""><figcaption></figcaption></figure>

Last step will be hosting this file in pinata by pressing **Upload** and selecting it:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.53.39 PM.png" alt=""><figcaption></figcaption></figure>

### Linking The Metadata to an NFT

Now that we have set up all the metadata, and have hosted this data successfully on pinata, let us make an Andromeda Application and link this data to an NFT.

Make a new empty project in the web-app and add a CW721 ADO to the App and publish it:

{% hint style="info" %}
If you havent gone through building a CW721 App before, you can find the steps in the previous guide "**Building your First NFT Collection**".
{% endhint %}

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 4.59.14 PM.png" alt=""><figcaption></figcaption></figure>

After publishing, it will appear in our Assets page:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 5.01.50 PM.png" alt=""><figcaption></figcaption></figure>

Select the **Mint** message from the list of execute messages for the CW721 to mint a new NFT. We have seen the fields for a Mint message before, so we can directly fill them in:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 5.04.11 PM.png" alt=""><figcaption></figcaption></figure>

Here is where it all comes together. We need to link our metadata file that we have stored on pinata to this NFT by inserting the URL for the file in the **Token Identifier** field. Once more we get the URL by inserting the files CID into "https://gateway.pinata.cloud/ipfs/{CID}":

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 5.06.52 PM.png" alt=""><figcaption></figcaption></figure>

In my case the URL is: [https://gateway.pinata.cloud/ipfs/QmWrM7KXRpRfLpjrfGRtYMgVgRGDmmWMPAJskeDfncfZzj](https://gateway.pinata.cloud/ipfs/QmWrM7KXRpRfLpjrfGRtYMgVgRGDmmWMPAJskeDfncfZzj)

Insert this into the Token Identifier and publish:

<figure><img src="../../../.gitbook/assets/Screen Shot 2024-04-26 at 5.09.43 PM.png" alt=""><figcaption></figcaption></figure>

The NFT is now complete and its metadata is correctly set up. Now, whenever this NFT is sent to a marketplace, or used in one of our embeddables, all the data for the NFT will be automatically taken from the URL we have provided.

### Conclusion

Metadata for NFTs is a very crucial part for NFT projects. Without it, the NFT would be basically useless. In our next examples, we will be looking at using some of our ADOs as a way to sell your NFTs with ease whether in an auction or marketplace.&#x20;
