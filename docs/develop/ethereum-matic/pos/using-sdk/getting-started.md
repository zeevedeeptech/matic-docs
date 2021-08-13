---
id: getting-started
title: Getting started with the Matic SDK
sidebar_label: Instantiating SDK
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## Quick Summary 

The matic.js SDK takes all the computing power of Polygon and places it right at your finger tip. With custom-made functions that allow for approval, deposit and wtihdraw, all without doing too much footwork, our reason for engineering this was to ensure you can get instant value from our platform. 

## Installation
The first step to using the awesome power of Polygon via our SDK is by doing an NPM install of it. Find here 

```bash
npm install @maticnetwork/maticjs
```

## Usage
Start by importing the SDK into your application with. 

```js
const MaticPOSClient = require('@maticnetwork/maticjs').MaticPOSClient

// or

import { MaticPOSClient } from '@maticnetwork/maticjs'
```
Done that? Next up, we use providers. The providers can be RPC urls or web3 based providers like Metamask provider, HDWalletProvider et.al based on what you want to see.

## RPC and HD Wallet

RPC 
```bash
const parentProvider = 'https://mainnet.infura.io/v3/<infura-key>'
const maticProvider = '<Infura now supports a dedicated Polygon RPC. See how to set that up here[https://blog.infura.io/polygon-now-available/](https://blog.infura.io/polygon-now-available/)>'
```

Next thing you want to do is instantiate the client by passing network details and providers based on the network you're integrating.
```js
// for mumbai testnet
const maticPOSClient = new MaticPOSClient({
  network: "testnet",
  version: "mumbai",
  parentProvider: <goerli-provider>,
  maticProvider: <mumbai-provider>
});
```
```js
// for Matic mainnet
const maticPOSClient = new MaticPOSClient({
  network: "mainnet",
  version: "v1",
  parentProvider: <ethereum-provider>,
  maticProvider: <matic-provider>
});
```
More details can be provided if you want to override the default values.
```js
const maticPOSClient = new MaticPOSClient({
  network: "testnet",
  version: "mumbai",
  parentProvider: <goerli-provider>,
  maticProvider: <mumbai-provider>,
  rootChain: <plasma-root-chain-address>,
  posRootChainManager: <pos-root-chain-manager-address>,
  posERC20Predicate: <pos-erc20-predicate-address>,
  posERC721Predicate: <pos-erc721-predicate-address>,
  posERC1155Predicate: <pos-erc1155-predicate-address>,
  parentDefaultOptions: { from: <user-address> },
  maticDefaultOptions: { from: <user-address> },
});
```

