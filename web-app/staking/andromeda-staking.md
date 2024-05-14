# Andromeda Staking

## Introduction

In this guide, we will be looking at how users can stake their Andromeda tokens and earn rewards for doing so. We will be using our custom interface for staking created by our team to make the process as seemless as possible. The interface can be accessed using the following link:&#x20;

{% embed url="https://staking.andromedaprotocol.io" %}

Here is a video that walks through the process of staking and other functionalities of our interface:

{% embed url="https://www.youtube.com/watch?t=37s&v=BZ_BHFahSRc" %}

### Benefits of Staking

Before we go ahead with the steps needed to stake, let us take a look at the benefits staking provides:

1. **Rewards:** The most obvious benefit for staking is the rewars that come with. Multiply your ANDR holding by accruing rewards over time.
2. **Governance:** Staking allows token holders to participate in the governance of the Andromeda network. When you have tokens staked, you can vote on proposals that affect the protocol, such as upgrades, parameter changes, or the allocation of network resources.
3. **Network Security:** Staking contributes to the security of the Andromeda network. Validators are required to stake a certain amount of tokens as collateral, which they can lose if they behave maliciously or fail to perform their duties correctly. This economic incentive ensures that validators have a vested interest in maintaining the integrity and security of the network.

### Connecting Your Wallet

The first thing you need to do after reaching the staking interface is connecting your wallet. To do so just press "**Connect Wallet**" in the top right of the page:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-09 at 4.53.07 PM.png" alt=""><figcaption></figcaption></figure>

Here we can see some boxes. Let us go through what each of these values mean:

* **Andr Staked:**  The amount of Andromeda tokens you have staked.

{% hint style="warning" %}
The APR is just an approximation. The formula for its calculation can be seen if you hover on the box.
{% endhint %}

* **APR (Annual Percentage Rate)**: An approximation to the amount of tokens to be received as rewards displayed as a percentage of the amount of staked tokens. For example if you have 100 Andr staked and the APR is 20%, then you would receive approximately 20 tokens as rewards throughout the year.

{% hint style="info" %}
Andromeda uses block rewards since it is a non-inflation chain.
{% endhint %}

* **Block Rewards:** The amount of ANDR tokens distributed as rewards per **Block**. This amount divided between all the stakers. &#x20;

{% hint style="info" %}
Unbonding period is only for the staked tokens. Rewards can be claimed immediately.
{% endhint %}

* **Unbonding Period:** The amount of time it takes to unstake tokens. For example, if you have 100 tokens staked and you want ot withdraw them, you need to wait 21 days to be able to do so.

### Stake Your Tokens

Now that you have connected your walet, you can directly stake your tokens. When staking, you will be delegating your tokens to one of the validators of the Andromeda chain. As you can see, there is a long list of validators to choose from. Next to each validator, there are two fields:

{% hint style="warning" %}
Staking enables voting on proposals for the staker. In case they do not cast a vote, then the vote of the validator they have delegated to is taken on their behalf.
{% endhint %}

* **Voting Power:** The percentage of staked tokens this validator has from the total amount staked. This includes all the staked tokens delegated to them by users.
* **Commission:** The percentage taken by the validator from your staking rewards as a fee.

To stake, simply press the "**Stake**" button of the validator you want to delegate your tokens to. Then select the amount of tokens to stake and confirm the transaction:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-09 at 6.33.15 PM.png" alt=""><figcaption></figcaption></figure>

Once the transaction has been confirmed, then the amount will show up in the **ANDR Staked** box. You can also see that the **Claimable Rewards** is no longer 0:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-09 at 6.37.07 PM.png" alt=""><figcaption></figcaption></figure>

### Claiming Rewards

{% hint style="warning" %}
You need to have some ANDR in order to claim your rewards to be used as fees for the transaction.
{% endhint %}

To claim rewards, simply press the "**Claim"** button from the "**Claimable Rewards**" box.&#x20;

### Undelegating Tokens

Lets say you have staked for a while and are now looking to unstake the tokens and have them back in your wallet. To do so we go to the **Delegated** tab to view all our delegated tokens:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-09 at 6.42.51 PM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
You need to have some ANDR in order to undelegate your tokens to be used as fees for the transaction.
{% endhint %}

Select the validator you want to undelegate from and then press "**Undelegate**". Confirm the transaction. Once undelegated, you can go to the "**Unbonding**" tab to view when the tokens will be released:

<figure><img src="../.gitbook/assets/Screen Shot 2024-05-09 at 7.02.44 PM.png" alt=""><figcaption></figcaption></figure>
