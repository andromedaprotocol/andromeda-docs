# Staking and Rewards

## What is Staking

Staking is the process of locking up an amount of tokens (ANDR) for a certain period of time. The tokens cannot be transfered or swaped during that time. In return, the staker will receive rewards which can be claimed directly and used as the staker sees fit.&#x20;

## How to Stake Tokens

Staking can be done very easily with our custom interface which has all what the user need to manage their tokens. The interface can be found here:

{% embed url="https://staking.andromedaprotocol.io" %}

## Andromeda Staking Mechanism

The ANDR token system is non-inflationary (no new ANDR tokens can be minted); founded on the principle that everything needs to be paid for by one entity or another, not just creating new tokens to solve a problem. The staking program aims to support validators through a transparent and finite token emission process, moving away from inflationary systems commonly found in blockchain networks.

The Andromeda DAO itself has a large reserve of tokens within the protocol treasury. By design the community itself is empowered to shape the best use of these network resources to drive sustainable growth.

The rewards pool is a module account, where rewards can be received from any address, but will most likely be received from the community pool as incentivization to stakers and validators on the Andromeda Chain. The Rewards Pool then distributes with other rewards (fees) these rewards to validators according to the voting power.&#x20;

The rewards are set to be distributed at a certain emission rate until:&#x20;



<figure><img src="../.gitbook/assets/Screen Shot 2024-04-22 at 5.10.51 PM.png" alt=""><figcaption></figcaption></figure>

When this is the case, distribution of other fees stays functioning as usual. The emission per block/drip rate value should be fetched as a parameter from chain parameters, which can then be changed using a governance proposal if needed.

## Drip Rate

Reward tokens are dripped out of the rewards account to the standard distribution module and participants in the staking program. If that rewards account drips all the rewards and is empty, then rewards to validators will also stop and require revisiting via additional governance proposals.&#x20;

## Staking Rewards

The DAO will vote to fund the **Rewards Account/Pool** that is also managed and controlled by the DAO. This provides for a budgeted amount of tokens that will be used to “drip” or pay the validators on a block by block basis.&#x20;

A key consideration should be to consider the financial stability of validators ensuring that their role in securing the network is sustainable and incentives are appropriate for their contribution to the protocol.

## Token Unbonding

Unbonding or Unstaking refers to the process of returning the staked tokens back to the user. Each chain has an unbonding period, which is the time it will take for the staked tokens to be released back to the user where he can swap, transfer, or do whatever he wants with the tokens. The unbonding period for the Andromeda Chain is 21 days.&#x20;
