---
id: deposit-withdraw-event-pos
title: Deposit and Checkpoint Event Tracking - PoS
sidebar_label: Deposit and Checkpoint Event Tracking
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

## Quick Summary:

This section of the docs deal with tracking and monitoring the pace and speed of transactions done within the Polygon ecosystem. Depositing into the network (when done with the PoS bridge) typically takes an average of 5-7 minutes but we've seen instances where users seek to see real time progress reports. As a developer, you may also want to augment the  UX of your app with instant feedback to the user. In all these cases, look into this section, we have exactly what you need.

## Deposit Events

When a user deposits a token from Ethereum to Polygon, the [State Sync Mechanism](https://docs.matic.network/docs/contribute/state-sync/state-sync/#:~:text=State%20Sync%20is%20the%20native,logic%20sits%20inside%20onStateReceive%20function.) is triggered. If you didn't click on that link, the state sync mechanism is simply the native mechanism that reads Ethereum data from Polygon EVM chain and establishes a balance between the two chains. Over a process that lasts for 5-7 minutes, the mechanism mints the equivalent value of the Ethereum tokens deposits on the Polygon chain for the user. As this takes a small amount of time, it is sometimes good UX to listen to the event stream and track it to see when it starts and ends.

## Realtime deposit event tracking using a web socket connection

The fastest route to seeing the stream of the connection and intermittently checking to see its process is via a WebSocket protocol. This is what your sample script should look like

```jsx
const WebSocket = require("ws");
const Web3 = require("web3");

// For Mumbai
const ws = new WebSocket("wss://ws-mumbai.matic.today/");
// For Matic mainnet: wss://ws-mainnet.matic.network/
const web3 = new Web3();
const abiCoder = web3.eth.abi;

async function checkDepositStatus(
  userAccount,
  rootToken,
  depositAmount,
  childChainManagerProxy
) {
  return new Promise((resolve, reject) => {
    ws.on("open", () => {
      ws.send(
        `{"id": 1, "method": "eth_subscribe", "params": ["newDeposits", {"Contract": ${childChainManagerProxy}}]}`
      );

      ws.on("message", (msg) => {
        const parsedMsg = JSON.parse(msg);
        if (
          parsedMsg &&
          parsedMsg.params &&
          parsedMsg.params.result &&
          parsedMsg.params.result.Data
        ) {
          const fullData = parsedMsg.params.result.Data;
          const { 0: syncType, 1: syncData } = abiCoder.decodeParameters(
            ["bytes32", "bytes"],
            fullData
          );

          // check if sync is of deposit type (keccak256("DEPOSIT"))
          const depositType =
            "0x87a7811f4bfedea3d341ad165680ae306b01aaeacc205d227629cf157dd9f821";
          if (syncType.toLowerCase() === depositType.toLowerCase()) {
            const {
              0: userAddress,
              1: rootTokenAddress,
              2: depositData,
            } = abiCoder.decodeParameters(
              ["address", "address", "bytes"],
              syncData
            );

            // depositData can be further decoded to get amount, tokenId etc. based on token type
            // For ERC20 tokens
            const { 0: amount } = abiCoder.decodeParameters(
              ["uint256"],
              depositData
            );
            if (
              userAddress.toLowerCase() === userAccount.toLowerCase() &&
              rootToken.toLowerCase() === rootTokenAddress.toLowerCase() &&
              depositAmount === amount
            ) {
              resolve(true);
            }
          }
        }
      });

      ws.on("error", () => {
        reject(false);
      });

      ws.on("close", () => {
        reject(false);
      });
    });
  });
}

// Param1 - user address
// Param2 - contract address on main chain
// Param3 - amount deposited on main chain
// Param4 - child chain manager proxy address (0xA6FA4fB5f76172d178d61B04b0ecd319C5d1C0aa for mainnet)
checkDepositStatus(
  "0xFd71Dc9721d9ddCF0480A582927c3dCd42f3064C",
  "0x47195A03fC3Fc2881D084e8Dc03bD19BE8474E46",
  "1000000000000000000",
  "0xb5505a6d998549090530911180f38aC5130101c6"
)
  .then((res) => {
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });
```

### Historical Checkpoint Inclusion Check by querying the blockchain

This can be checked using the following API. The block number of the burn transaction on the child chain has to be given as a param to this GET API.

```jsx
// Testnet
https://apis.matic.network/api/v1/mumbai/block-included/block-number
// Mainnet
https://apis.matic.network/api/v1/matic/block-included/block-number
```