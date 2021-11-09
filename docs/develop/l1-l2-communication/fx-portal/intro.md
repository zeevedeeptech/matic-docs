---
id: intro
title: Introduction
description: Introducing the Fx-Portal. Fast, easy communication without explicit mapping
keywords:
  - docs
  - polygon
image: https://matic.network/banners/matic-network-16x9.png
---

## Quick Summary

The FxPortal is Polygon's permissionless, highly flexible meta-bridge built on top of the State Sync mechanism that facilitates the movement of data across chains. With the FxPortal, there is no need for explicit mapping between the child and root contract, or mapping of any sort really. The FxPortal uses the State Sync and Checkpoint mechanisms to orchestrate a cross-chain transfers using the withdrawal and deposit functions of the tunnel. 

Why not the PoS bridge as Polygon as we architected it? Well, its simple. We wanted to create the very best and flexible system for you to move data while ensuring that you can customize the experience as far as you want to. FxPortal also provides an alternative for where ERC standardized tokens can be deployed without any mapping required, all done and completed by the base FxPortal Contracts. But we'll come to them in a second. For now, let's talk about how the FxPortal works.

## How does the FxPortal work?

The FxPortal works through two contracts, the FxChild and the FxRoot, that communicate via passing information through the StateSync Mechanism and the use of what we call tunnelling contracts. In Polygon, our tunneling contracts are the [FxBaseRootTunnel](https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseRootTunnel.sol) and the [FxBaseChildTunnel](https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseChildTunnel.sol) which form the data tunnel mechanism. The Polygon team has deployed these contracts for you so you can get a jump start on the project, but if you want to start your own way, you can go ahead to extend the tunneling contracts to include your custom logic.

## Talk is cheap, show me the code

In the repositories underneath, you'll find the architectural patterns we use for already deployed contracts on Ethereum and Polygon. If you check, they are not mapped to each other; all they do is communicate with each other by making use of the FxRoot and FxChild contracts. 

[https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseRootTunnel.sol](https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseRootTunnel.sol) - FxBaseRoot (Root contract on Ethereum) 

[https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseChildTunnel.sol](https://github.com/fx-portal/contracts/blob/main/contracts/tunnel/FxBaseChildTunnel.sol) - FxBaseChild (Child contract on Polygon) 

## Walk me through the code please

The **sendMessageToChild** function on the **FxBaseRootTunnel** is called and then this function calls the **[SyncState](https://github.com/maticnetwork/contracts/blob/53deec8548b01b85c083f59524d8322643518da8/contracts/root/stateSyncer/StateSender.sol#L33)** function. The state sync happens and the **[State Sync** event is emitted](https://github.com/maticnetwork/contracts/blob/53deec8548b01b85c083f59524d8322643518da8/contracts/root/stateSyncer/StateSender.sol#L38) and listened to by the Heimdall layer. As soon as this is done, the validators trigger the onStateReceive function on the **[FxChild](https://github.com/fx-portal/contracts/blob/main/contracts/FxChild.sol)** which goes on to decode the message and then calls the **processMessageFromRoot** function where all your logic then happens. Easy as that!