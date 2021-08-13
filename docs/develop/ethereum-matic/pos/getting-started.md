---
id: getting-started
title: PoS Bridge
sidebar_label: Introduction
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---
import useBaseUrl from '@docusaurus/useBaseUrl';

## Quick Summary

The Proof of Stake bridge (PoS) enables seamless control over assets, faster deposit/withdrawal, and flexibility. As a means of transfer of assets, it is perfect for dApps and integrations that are content with security provided by a robust set of external validators.


## Introduction

Moving in and out of the Polygon ecosystem is exponentially faster with the PoS bridge and this guide exists to show you just how easy it is. With our validator-run token bridge, you can move your ERC20, ERC721, and ERC1155 tokens at speeds never seen before. Unlike the plasma bridge with a 7-day withdrawal period, the PoS bridge completes deposits within 7-8 minutes and withdrawals within 30 minutes. Interested in how we can help you scale? Let's show the steps below.


## Steps to use the PoS Bridge

Before we enter into this section of the docs, it may help to have a thorough understanding of these terms as you'll interact with them while trying to use the bridge. [Mapping](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/) and the [State Sync Mechanism](https://docs.matic.network/docs/validate/validator/state-sync-mechanism/) 

Done with those links? Let's continue to a high level overview of the flow then.

- The first step to using the PoS bridge is mapping the **Root Token** and **Child Token**. Don't worry, this isn't anything complex. It just means that the token contract on the root chain and the token contract on the child chain have to maintain a connection (called mapping) to transfer assets between themselves. If you're interested in submitting a mapping request, please do that [here](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/).

At a lower level and with more detail, this is what happens

### Deposit

- The owner of the asset token approves the Predicate Contract to lock down the amount of tokens to be deposited. Once this approval transaction has confirmed, the owner of the asset token interacts with the RootChainManager contract to complete the deposit.

- Next up, the asset is deposited with the State Sync Mechanism, if you didn't get a run-through of what the State Sync Mechanism is, it's in its simplest form the native mechanism to send data from Ethereum Network to the Polygon Network. The inner workings of the mechanism itself comprises of a function call that is made of the RootChainManager which triggers the ChildChainManager contract. 

Want to see this in video format? Please check it out below

<video loop autoplay width="70%" height="70%" controls="true" >
  <source type="video/mp4" src="/img/matic-to-eth/matic-eth-deposit.mp4"></source>
  <p>Your browser does not support the video element.</p>
</video>

### Withdrawls

- Withdrawing assets is a breeze with the PoS bridge. It's as simple as burning the asset tokens on the Polygon chain, collecting the transaction hash of this burn transaction, and submitting it to the **RootChainManager**. The **RootChainManager** then calls for the predicate contract to release the funds that were locked on the Ethereum chain.

- Once the burn transaction is validated on the Polygon chain, it takes 30 minutes to 3 Hours for this burn transaction to be checkpointed. Checkpointing is the process of merging the Polygon transactions into the Ethereum blockchain.

- Next up, the proof of this burn transaction is submitted to the **RootChainManager** by calling the exit function. This function call takes in the burnHash for verifying the checkpoint inclusion and only then triggers the Predicate Contract which unlocks and releases the funds that were deposited.

Want to watch all of this in video form? Please check it out below

<video loop autoplay width="70%" height="70%" controls="true" >
  <source type="video/mp4" src="/img/matic-to-eth/matic-eth-withdraw.mp4"></source>
  <p>Your browser does not support the video element.</p>
</video>

and that is all!

Next up, we're going to deal with deposits and withdrawals using the Matic.js SDK. Stay tuned for the ride.