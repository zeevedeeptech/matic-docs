---
id: state-sync-mechanism
title: State Sync Mechanism
description: Learn about the state sync mechanism on Polygon to natively read Ethereum data.
keywords:
  - docs
  - matic
  - polygon
  - state sync
image: https://matic.network/banners/matic-network-16x9.png 
---

Validators on the [Heimdall](/docs/validate/glossary#heimdall) layer pick up the [StateSynced](https://github.com/maticnetwork/contracts/blob/a4c26d59ca6e842af2b8d2265be1da15189e29a4/contracts/root/stateSyncer/StateSender.sol#L24) event and pass the event on to the [Bor](/docs/validate/glossary#bor) layer. See also [Polygon Architecture](/docs/contribute/polygon-architecture).

The receiver contract inherits [IStateReceiver](https://github.com/maticnetwork/genesis-contracts/blob/master/contracts/IStateReceiver.sol), and custom logic sits inside the [onStateReceive](https://github.com/maticnetwork/genesis-contracts/blob/05556cfd91a6879a8190a6828428f50e4912ee1a/contracts/IStateReceiver.sol#L5) function.

The state sync mechanism requirements for the users:

Things required from dapps/users to work with state-sync:

1. Call the [syncState](https://github.com/maticnetwork/contracts/blob/19163ddecf91db17333859ae72dd73c91bee6191/contracts/root/stateSyncer/StateSender.sol#L33) function.
1. The `syncState` function emits an event called `StateSynced(uint256 indexed id, address indexed contractAddress, bytes data);`
1. All the validators on the Heimdall chain receive the `StateSynced` event. Any validator that wishes to get the transaction fee for the state sync sends the transaction to Heimdall.
1. Once the `state-sync` transaction on Heimdall is included in a block, it is added to the pending state-sync list.
1. After every sprint on Bor, the Bor node fetches the pending state-sync events from Heimdall via an API call.
1. The receiver contract inherits the `IStateReceiver` interface, and custom logic of decoding the data bytes and performing any action sits inside the [onStateReceive](https://github.com/maticnetwork/genesis-contracts/blob/master/contracts/IStateReceiver.sol) function.
