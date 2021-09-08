---
id: erc20
title: ERC20
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - polygon
image: https://matic.network/banners/matic-network-16x9.png
---

## Quick Summary

ERC20 tokens transferred through the FxPortal can take advantage of the tunnel mechanism that allows for deposit of tokens on the root chain and withdrawal on the child chain. This specially crafted tunnels are the **FxERC20RootTunnel** and the **FxERC20ChildTunnel**. With these tunnels, mapping is automated, state sync is triggered and messages are passed from one chain to the other. Let's talk about these tunnels and the most important functions inside them, shall we?

## FxERC20RootTunnel

The [**FxERC20RootTunnel**](https://github.com/fx-portal/contracts/blob/main/contracts/examples/erc20-transfer/FxERC20RootTunnel.sol) uses the power of the FxPortal to send and receive ERC20 tokens from the child to the root chain. This tunnel uses the FxRoot, the FxChild, and a couple of other helper functions to move the tokens from one end to the other. 

Here are some important functions that should be known if you're using the **FxERC20RootTunnel**

- `deposit(address rootToken, address user, uint256 amount, bytes memory data`he `deposit()` function is called with the address of the token on root chain, the receivers address, the amount and the data if needed. You must have approved the contract using the standard ERC20 `Approve` function to spend your tokens first.
- _`processMessageFromChild(bytes memory data)` : The receiveMessage function calls the `_processMessageFromChild()` function which is the exit function for the **FxERC20RootTunnel**. The `_processMessageFromChild()`function is called with the data that was successfully transferred from the Child chain and then it goes on to transfer or release the tokens to the receiver's account.

## FxERC20ChildTunnel

The [FxERC20ChildTunnel](https://github.com/fx-portal/contracts/blob/main/contracts/examples/erc20-transfer/FxERC20ChildTunnel.sol) is the contract that is deployed on the child chain to facilitate the withdrawal and deposit of ERC20 tokens. Here are some important functions that should be known if you're using the **FxERC20ChildTunnel**

- `withdraw(address childToken, uint256 amount)`: The `withdraw()` function is used to remove all the coins assigned to the address in the `address` parameter of the deposit function. The address will receive the child token created when first mapped.
- `_processMessageFromRoot(uint256, address sender, bytes memory data)`: The processMessageFromRoot function takes in the address of the sender and the data that is passed

## Deposit

### Steps for ERC20 transfer from Ethereum to Polygon

1. Deploy your own ERC20 token on the root chain. You will need this address later.
2. Approve the tokens for transfer by calling the `approve()` function of the root token with the address of the **FxERC20RootTunnel** and the amount as the arguments.
3. Proceed to call `deposit()` with the address of the receiver and amount on the root chain to receive the equivalent child token on the child chain. This will also map the token automatically. Alternatively, you can call `mapToken()` first before depositing.
4. That's it! 🎉 After mapping, you should now be able to execute cross-chain transfers using the deposit and withdraw functions of the tunnel.

**Note:** After you have performed deposit() on the root chain, it will take 7-8 minutes for state sync to happen. Once state sync happens, you will get the tokens deposited at the given address.

### How to initiate an ERC20 Deposit through the FxPortal

1. Pass the contract address of the `rootToken`, the address of the user, the amount to be deposit and any extra data into the deposit function of the **FxERC20RootTunnel** as [seen here](https://github.com/fx-portal/contracts/blob/3190bdcc4f74ad58324599dcf57c57bee66d1164/contracts/examples/erc20-transfer/FxERC20RootTunnel.sol#L57). The next line checks for root to child token mapping to see if it is present o f not, and if it isn't present, the `mapToken()` function is called which registers the mapping on the root and child tunnels
2. The tokens are locked on the root chain side, in this case the Ethereum side as [seen here](https://github.com/fx-portal/contracts/blob/3190bdcc4f74ad58324599dcf57c57bee66d1164/contracts/examples/erc20-transfer/FxERC20RootTunnel.sol#L64) 
3. The `_sendMessageToChild()` function is called. This triggers a second state sync which in turn triggers the `_syncDeposit()` function on the [FxERC20ChildTunnel](https://github.com/fx-portal/contracts/blob/main/contracts/examples/erc20-transfer/FxERC20ChildTunnel.sol). 
4. The coins are minted on the [child chain here](https://github.com/fx-portal/contracts/blob/3190bdcc4f74ad58324599dcf57c57bee66d1164/contracts/examples/erc20-transfer/FxERC20ChildTunnel.sol#L98)

And that's it! Deposit is done!

It must be understood that the mapping always happens in order. If a mapping isn't done before hand, the mapping stateSync happens and then the deposit stateSync happens. The power of the FxPortal isn't that mapping doesn't happen, its that mapping isn't a prerequisite, and it can be done with the process of minting coins

### Can you show me how the deposit code moves in real-time?

Go to the example contracts list, copy the FxERC20RootTunnel contract address, paste it on [Goerli's Etherscan](http://goerli.etherscan.io/), go the the contracts tab and walk through the code like this

- The `FxERC20RootTunnel()` ****function on line 1295 internally calls `deposit` function which in itself calls `_sendMessageToChild()`
- `_sendMessageToChild()` on line 1109 then calls the `sendMessageToChild()` function of the FxRoot and takes in the `FxChildTunnel` and `message` as parameters

Go [here](https://github.com/fx-portal/contracts/blob/main/contracts/FxRoot.sol) to continue 

- FxRoot does a stateSync call and the state sync event happens
- FxChild onStateRecieve is called which then passes the message to the "reciever" which is the `FxERC20ChildTunnel`

Go [here](https://github.com/fx-portal/contracts/blob/main/contracts/examples/erc20-transfer/FxERC20ChildTunnel.sol) to continue

- The FxERC20ChildTunnel has the processMessageFromRoot Function which recieves amongst other things, the senders address and the data. This data is checked for its type - deposit or map_token. Deposit goes on to trigger the `_syncDeposit` function that then goes on to call the `mint` function of the **childTokenContract.**
- That's it! Deposit is done!

## Withdrawal

### Steps for ERC20 transfer from Polygon to Ethereum

1. Proceed to call `withdraw()` with the respective token address and amount as arguments on the child contract to move the child tokens back to the designated receiver on the root chain. **Note the tx hash** as this will be used to generate the burn proof.

### Can you show me how the withdrawal code moves in real time?

1. Call the `withdraw()` function on the **FxERC20ChildTunnel.** This takes in the `childToken` address and the `amount`
2. The `withdraw()` function in turn calls the `burn()` 🔥 on the `childTokenContract` contract [here](https://github.com/fx-portal/contracts/blob/3190bdcc4f74ad58324599dcf57c57bee66d1164/contracts/examples/erc20-transfer/FxERC20ChildTunnel.sol#L43)
3. The `_sendMessageToRoot()` function is called, checkpoint happens and the proof from the burn is submitted here.

Copy this (0xbfDeFCd92335b22b205bb5b63B9eC909D6e99C16), and continue [here](https://goerli.etherscan.io/address/0xbfDeFCd92335b22b205bb5b63B9eC909D6e99C16#code)

1. On line 1232, we have the `processMessageFromChild`. The `recieveMessage()` verifies check point inclusion of the child token transaction then calls the `processMessageFromChild` function. This function decodes the amount of tokens that was burnt and then transfers the amount back to the user. 
2. And that is it! 

receiveMessage function verifies check point inclusion of the child token transaction and then calls _processMessageFromChild