---
id: erc20
title: ERC20 Deposit and Withdraw Guide
sidebar_label: ERC20
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

## Quick Summary

This section of the docs deals with how to deposit and withdraw ERC20 tokens on the Polygon network. Large parts of the documentation here are very much the same as the ETH section of the docs, and this is because ETH and ERC20 are the same in some respects and divergent in others. The most notable difference is that an ERC20 token is a standard used for creating and issuing smart contracts on the Ethereum blockchain. As can be expected, functions are different to reflect the standards, so please make sure to keep an eye on the methods you use for interacting with the SDK.

## Introduction

This tutorial uses the Polygon Testnet (Mumbai) which is mapped to the Goerli Network. For the purpose of this tutorial, we have already deployed the Test tokens and mapped them on the PoS bridge. You can request the asset from the [faucet](https://faucet.matic.network/) and if they aren't available, please reach out to us on [discord](https://discord.com/invite/er6QVj) and we'll get back to you immediately.

In the upcoming tutorial, every step will be explained in detail along with a few code snippets. However, you can always refer to this repository which will have all the example source code that can help you to integrate and understand the working of the PoS bridge.

## High Level Flow
### Deposit ERC20

- Approve the **ERC20PredicateProxy** contract to spend the tokens that are to be deposited.
- Make the **depositFor** call on the **RootChainManager** contract on Ethereum.

### Withdraw ERC20

- **Burn** your token on the Polygon chain. This involves sending your token to a bogus address so the token is no longer usable. 
- Call the **exit** function and make sure to submit the transaction proof of burn hash. This call is to be made after the checkpoint is submitted for the block containing burn transaction.

## Details and an Explanation of Terms

### Approve

The **ERC20PredicateProxy** contract is the beginning of the ERC20 deposit process. Approving this contract is important because it's what is responsible for locking our tokens by transferring the tokens to itself. It then goes on to call the **transferFrom** function. To facilitate this flow, the Polygon PoS client exposes the **approveERC20ForDeposit** method to make the call.

This is what the **approveERC20ForDeposit** method looks like:
```jsx
await maticPOSClient.approveERC20ForDeposit(rootToken, amount, { from });
```

### Deposit

The actual deposit happens at this step. The interaction is with the **RootChainManagerProxy** contract and the Tokens get locked into the **ERC20PredicateProxy** contract and the Polygon PoS client exposes the **depositERC20ForUser** method to make this call. Once the **deposit** transaction is confirmed, the **StateSync** Mechanism is triggered and it takes 7-8 minutes to be completed. Immediately, the **deposit** function of the **ChildToken** is called by the **ChildChainManager**. Tokens should be minted when this call is made.

This is what the **depositERC20ForUser** method looks like
```jsx
await maticPOSClient.depositERC20ForUser(rootToken, from, amount, {
  from,
  gasPrice: "10000000000",
});
```

Sidenote: Deposits from Ethereum to Matic happen using a state sync mechanism and takes about ~5-7 minutes. After waiting for this time interval, it is recommended to check the balance using web3.js/matic.js library or using Metamask. The explorer will show the balance only if at least one asset transfer has happened on the child chain. This [link](/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos) explains how to track the deposit events.

### Burn

To burn the tokens, call the **withdraw** function of the **ChildToken** contract. To do this, Polygon PoS client exposes the **burnERC20** method.

This is what the **burnERC20** method looks like

```jsx
await maticPOSClient.burnERC20(childToken, amount, { from });
```

Make sure to store the transaction hash for this call and use it while generating burn proof.

### Exit

Once the checkpoint has been submitted for the block containing burn transaction, please call the exit function of the RootChainManager contract and submit the proof of burn. Upon submitting valid proof tokens are transferred to the user. The Polygon PoS client exposes the exitERC20 method to make this call. This function can be called only after the checkpoint is included in the main chain. The checkpoint inclusion can be tracked by following this [guide](https://docs.matic.network/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos/#checkpoint-events)

The **exitERC20** method looks like this

```jsx
await maticPOSClient.exitERC20(burnTxHash, { from });
```
