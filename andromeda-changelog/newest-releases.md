# Newest Releases

## Andromeda Upgrades to v1.1                                                       6/16/24

{% hint style="info" %}
A [changelog](https://github.com/andromedaprotocol/andromeda-core/blob/development/CHANGELOG.md) can be found in our repo listing the changes and their corresponding PRs.
{% endhint %}

## Addition of Module ADOs to the System

Andromeda has introduced two new modules to the system. These modules can be used to further customize ADOs to meet a user's needs.&#x20;

### Rates Module

{% hint style="info" %}
By action, we are referring to a specific execute message.&#x20;
{% endhint %}

The rates module can be used to add a fee to be paid for performing an action. The most common use case for rates is adding taxes or royalties on NFT or CW20 sales. The rate can be added in one of two ways:

* **Locally:** Rate messages have been added to the set of base execute message and can be added through the ADO itself on an action.
* **Using the new Rates ADO:** A [Rates ADO ](broken-reference) has been developed that can be used to set up the rates for other ADOs. In this case, the ADO implementing rates needs to reference the Rates ADO that has been set up.

Rates can only be applied on ADOs that implement the "rates" feature. This will be specified in the ADO's documentation page.

Read all about the new Rates [here](broken-reference).

### Address List Module&#x20;

The [Address List ADO](broken-reference) can be used to set up permissioning for other ADOs. A user can set up the Address List ADO with all the permissions needed and then reference it from the ADO looking to implement these permissions.&#x20;

Read all about the new permissioning [here](broken-reference).

***

## New ADOs Released

### Staking ADO

The Staking ADO is a new addition to the Andromeda ADO arsenal. It is a simple but effective ADO that allows project owners to easily manage staking funds to the validator of their choice.&#x20;

This ADO is not meant for general use (Having public users stake funds), but is meant to be added to Andromeda Apps to extend their utility. Now any proceeds made by an Andromeda App can be easily and automatically rerouted to the Staking ADO to be staked. This opens up a big number of use cases for projects looking to build using the aOS.

Read all about the new Staking ADO [here](broken-reference).

### Primitive ADO

The new Primitive ADO is a contract designed to work similar to a database. It can be used to store key/value pairs. The primitive accepts the following types of data to be stored:

```rust
pub enum Primitive {
    Uint128(Uint128),
    Decimal(Decimal),
    Coin(Coin),
    Addr(Addr),
    String(String),
    Bool(bool),
    Binary(Binary),
}
```

Primitive can be set to be one of the following:

* **Private:** Only the owner is allowed to set/remove values.
* **Public:** Primitive is open to anyone similar to a public database.
* **Restricted:** Only the user that set the value is able to modify/remove it.

Read all about the new Primitive ADO [here](broken-reference).

### Conditional Spiltter

The Conditional Splitter ADO is a new type of splitter created by Andromeda to satisfy specific use cases for our users.&#x20;

It allows the owner of the ADO to specify multiple sets of recipient addresses to receive funds by the splitter. Depending on the amount sent, the splitter will automatically split the funds to the required set.&#x20;

Read all about the new Conditional Splitter ADO [here](newest-releases.md#conditional-spiltter).

### Crowdfund ADO Reworked

The Crowdfund ADO has been reworked to an improved version. The main goal of the ADO remains the same but changes were made to the logic to make the ADO better suited for crowdfunding.

{% hint style="info" %}
The new Crowdfund ADO will be available on testnet for now until it has passed the audit process.
{% endhint %}

***

## ADO Enhancements

### Added Ability to Remove and Replace Reward Tokens in CW20 Staking ADO

The CW20 Staking ADO has two new execute messages:

* [**RemoveRewardToken**](broken-reference)**:** Allows the owner to remove a previously set reward for the ADO.
* [**ReplaceRewardToken:**](broken-reference) Allows the owner to replace a reward token with another one.

### Added Expiry Struct

We have introduced a new struct for setting expirations and start times allowing users to chose between two options:

```rust
pub enum Expiry {
    FromNow(Milliseconds),
    AtTime(Milliseconds),
}
```

* **FromNow:**  Defines a duration in milliseconds from the current time. For example using 600000 means the time will be reached in 10 min from now.
* **AtTime:** Defines a [timestamp](https://www.epochconverter.com) in milliseconds.

The Expiry struct has been inserted in many of our ADOs where needed. You can read more about it [here](broken-reference).

### Added Asset Struct

A new enum for specifying the type of asset has been introduced. It is used in cases where either native or CW20 can be used:

```rust
pub enum Asset {
    Cw20Token(AndrAddr),
    NativeToken(String),
}
```

* **Cw20Token:** Used to specify that the asset being used is a CW20 token. Specify the ADO address in this case.
* **NativeToken:** Used to specify that the asset being used is a native token. Specify the micro-denomination in this case.

This enum has been placed in ADOs where needed like the auction, marketplace and many more.&#x20;

### Added ADOBaseVersion Query to the Base Queries

A new query has been added to the base queries. The ADOBaseVersion query is used to retrieve the version of the ADOBase.

Read more about this query [here](broken-reference).

## Audits

Two ADOs went through audit from [Sherlock](https://www.sherlock.xyz):

* Vesting ADO
* Staking ADO

## CLI

Minor bug fixes and improvements were made to the Andromeda CLI:

* CLI closing when a non valid option is inputed bug fixed
* Added Exit option for all commands
* Added the new integrated chain configs to the CLI

### Documentation

* New doc pages for the new ADOs were added&#x20;
* Documentation for  running a node on chain was added
* Documentation for the staking interface was added

## Integrations

The aOS has been integrated into several new chains on mainnet:

* [Terra](https://www.terra.money)
* [Archway](https://docs.archway.io)
* [Neutron](https://www.neutron.org)

## Web Application and UI Updates

### Schema Updates&#x20;

Updates and improvements have been made to many of our ADO panels making the process clearer and more intuitive for our users.

Several other enhancements were added to the web-app:

* Added a new refresh button in the embeddables page
* Launch page of web-app revised&#x20;
* Fixed bug that was occuring when reloading a flex file

### Asset Browser&#x20;

Several updates were made making asset browser easier and clearer then ever. A rundown of the new asset explorer:

{% embed url="https://youtu.be/w_0kmLV7XfM" %}

### Staking Interface

{% hint style="info" %}
Several UI updgrades were done to the staking interface for a better user experience.
{% endhint %}

The following features were introduced to the Andromeda staking interface:

{% embed url="https://staking.andromedaprotocol.io" %}

* Added the ability to delegate to validators not in the active set
* Added redelegation option
* Added a "Stake More" button in the user's "Delegated" tab
* Rewards are now shown for each validator instead of just the total
* UI improvements for a better user experience



## HackerBoard Release

Andromeda has launched its own hackerboard for devs looking to build. Tasks are available for devs to apply with bounties attached to each successfull completion. Devs can also apply to develop an ADO to be added to the aOS:

{% embed url="https://www.andromedaprotocol.io/aos-hackerboard" %}

## Archway Hackathon

Andromeda hackathon on Archway took place with some amazing teams and develpors:

{% embed url="https://dorahacks.io/hackathon/hunt-a-thon/buidl" %}
