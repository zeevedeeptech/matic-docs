---
id: eth
title: ETH Deposit and Withdraw Guide
sidebar_label: ETH
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

## Introduction

This guide uses the Polygon Testnet (Mumbai) which in itself is mapped to the Goerli Network to demonstrate asset transfer between the two blockchains. It's important to note that for the purposes of this tutorial, you should use a proxy address whenever is possible. This is because the implementation contract address is liable to change when a new update is added to the contract code. The proxy never changes and the proxy redirects all the incoming calls to the latest implementation. If you use the proxy address, you won't need to worry about any changes happening on the implementation contract before you're ready.

For example, please use the **RootChainManagerProxy** address for interactions instead of the **RootChainManager** address. Deployment details like the PoS contract addresses, ABI, Test Token Addresses can be found [here](https://docs.matic.network/docs/develop/ethereum-matic/pos/deployment/)

Mapping your assets is a necessary step for integrating the PoS bridge on your application so if you haven't done it, please submit a mapping request [here](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/). For the purposes of this tutorial, the team has deployed test tokens and mapped them to the PoS bridge. Request the asset you want to use on the [faucet](https://faucet.matic.network/) and if the test tokens are unavailable, reach out to the team on [Discord](https://discord.com/invite/er6QVj). We'll make sure to reply you immediately. 



## High Level Flow

### Deposit ETH

- Call the **depositEtherFor** function on the **RootChainManager** and send the required Ether.

### Withdraw ETH

- Burn your token on the Polygon chain. This involves sending your token to [Polygon's zero address](https://polygonscan.com/address/0x0000000000000000000000000000000000000000) so the token is no longer usable. 

- Call **exit** function on **RootChainManager** to submit the transaction proof of burn hash. This call can be made after the **checkpoint** is submitted for the block containing burn transaction.

## Details and an Explanation of Terms

### Deposit

ETH can be deposited to the Polygon chain by calling **depositEtherFor** on the RootChainManager contract. The Polygon PoS client exposes the **depositEtherForUser** method to make this call. Since ETH is deposited as ERC20 on the Polygon chain, withdrawing it follows the same process as ERC20 tokens.

The **depositEtherForUser** method looks like this:

```jsx
await maticPOSClient.depositEtherForUser(from, amount, {
  from,
  gasPrice: "10000000000",
});
```

Sidenote: Deposits from Ethereum to Polygon happen using the state sync mechanism and this takes about 5-7 minutes. After waiting for this time interval, it is recommended to check the balance using web3.js/matic.js library or using Metamask. The explorer will show the balance only if at least one asset transfer has happened on the child chain. This [link](https://docs.matic.network/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos/) explains how to track the deposit events.

### Burn

To burn the tokens and engage the withdrawal process, please call the **withdraw** function of the MaticWETH contract. Since Ether is an ERC20 token on the Polygon chain, using the **burnERC20** method that the Polygon PoS client exposes kickstarts the process.

The **burnERC20** method looks like this

```jsx
await maticPOSClient.burnERC20(childToken, amount, { from });
```

Make sure you store the transaction hash returned from this call because you will use this while generating burn proof.

### Exit

Once the checkpoint has been submitted for the block containing burn transaction, please call the exit function of the **RootChainManager** contract and submit the proof of burn. Upon submitting valid proof, tokens are transferred to the user. The Polygon PoS client exposes the **exitERC20** method to make this call. This function can be called only after the checkpoint is included in the main chain. The checkpoint inclusion can be tracked by following this [guide](/docs/develop/ethereum-matic/pos/deposit-withdraw-event-pos#checkpoint-events).

The **exitERC20** method looks like this

```jsx
await maticPOSClient.exitERC20(burnTxHash, { from });
```
