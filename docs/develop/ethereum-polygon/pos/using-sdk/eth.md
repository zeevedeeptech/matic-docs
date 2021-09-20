---
id: eth
title: ETH Deposit and Withdraw Guide
sidebar_label: ETH
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---
## Quick Summary

This section of the docs deals with how to deposit and withdraw ERC20 tokens on the Polygon network. Common functions exist between the ETH, ERC20, ERC721 and ERC1155 sections of the docs with variances in the naming and implementation patterns as befitting the standards. The most important prerequisite to using this section of the docs is mapping your assets, so please submit your mapping request [here](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/).

## Introduction

This guide uses the Polygon Testnet (Mumbai) which in itself is mapped to the Goerli Network to demonstrate asset transfer between the two blockchains. It's important to note that for the purposes of this tutorial, you should use a proxy address whenever is possible. This is because while the implementation contract address is liable to change when a new update is added to the contract code, the proxy never changes and it redirects all the incoming calls to the latest implementation. In essence, if you use the proxy address, you won't need to worry about any changes happening on the implementation contract before you're ready.

For example, please use the **RootChainManagerProxy** address for interactions instead of the **RootChainManager** address. Deployment details like the PoS contract addresses, ABI, Test Token Addresses can be found [here](https://docs.matic.network/docs/develop/ethereum-matic/pos/deployment/)

Mapping your assets is a necessary step for integrating the PoS bridge on your application so if you haven't done it, please submit a mapping request [here](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/). For the purposes of this tutorial, the team has deployed test tokens and mapped them to the PoS bridge. Request the asset you want to use on the [faucet](https://faucet.matic.network/) and if the test tokens are unavailable, reach out to the team on [Discord](https://discord.com/invite/er6QVj). We'll make sure to reply you immediately. 

**Mapping your assets** is necessary to integrate the PoS bridge on your application. You can submit a mapping request [here](/docs/develop/ethereum-matic/submit-mapping-request). But for the purpose of this tutorial, we have already deployed the **Test tokens** and Mapped then on the PoS bridge. You may need it for trying out the tutorial on your own. You can request the desired Asset from the [faucet](https://faucet.matic.network/). If the test tokens are unavailable on the faucet, do reach us on [discord](https://discord.gg/er6QVj)

In the upcoming tutorial, every step will be explained in detail along with a few code snippets. However, you can always refer to this [repository](https://github.com/maticnetwork/matic.js/tree/v2.0.2/examples/POS-client) which will have all the **example source code** that can help you to integrate and understand the working of PoS bridge.

## High Level Flow

Deposit ETH -

1. Make **_depositEtherFor_** call on **_RootChainManager_** and **send **the required ether.

Withdraw ETH -

1. **_Burn_** tokens on Polygon chain.
2. Call **_exit_** function on **_RootChainManager_** to submit proof of burn transaction. This call can be made **_after checkpoint_** is submitted for the block containing burn transaction.

## Steps

### Deposit

ETH can be deposited to the Polygon chain by calling **depositEtherFor** on the **RootChainManager** contract. The Polygon PoS client exposes the **depositEtherForUser** method to make this call. Since ETH is deposited as ERC20 on the Polygon chain, withdrawing it follows the same process as ERC20 tokens.

**_ETH_** is deposited as **_ERC20_** token on Polygon chain. For withdrawing it follow the same process as withdrawing ERC20 tokens.

```jsx
await maticPOSClient.depositEtherForUser(from, amount, {
  from,
  gasPrice: "10000000000",
});
```

Sidenote: Deposits from Ethereum to Polygon happen using the **State Sync** Mechanism and this takes about 5-7 minutes. After waiting for this time interval, it is recommended to check the balance using web3.js/matic.js library or using Metamask. The explorer will show the balance only if at least one asset transfer has happened on the child chain. This [link](https://docs.matic.network/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos/) explains how to track the deposit events.

### Burn

To burn the tokens and engage the withdrawal process, please call the **Withdraw** function of the MaticWETH contract. Since Ether is an ERC20 token on the Polygon chain, using the **burnERC20** method that the Polygon PoS client exposes kickstarts the process.

The **burnERC20** method looks like this

```jsx
await maticPOSClient.burnERC20(childToken, amount, { from });
```

Store the transaction hash for this call and use it while generating burn proof.

### Exit

Once the **checkpoint** has been submitted for the block containing burn transaction, user should call the **exit** function of `RootChainManager` contract and submit the proof of burn. Upon submitting valid proof tokens are transferred to the user. Polygon POS client exposes `exitERC20` method to make this call. This function can be called only after the checkpoint is included in the main chain. The checkpoint inclusion can be tracked by following this [guide](/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos#checkpoint-events).

```jsx
await maticPOSClient.exitERC20(burnTxHash, { from });
```
