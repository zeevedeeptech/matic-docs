---
id: mintable-erc20
title: Mintable ERC20 tokens on Fxportal
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - polygon
image: https://matic.network/banners/matic-network-16x9.png
---

### FxMintableERC20RootTunnel

- `deposit(address rootToken, address user, uint256 amount, bytes memory data)`: To deposit tokens from Ethereum to Polygon
- `receiveMessage(bytes memory inputData)`: Burn proof to be fed as the inputData to receive tokens on the root chain

### FxMintableERC20ChildTunnel

- `deployChildToken(uint256 uniqueId, string memory name, string memory symbol, uint8 decimals)`: To deploy a ERC20 token on Polygon chain
- `mintToken(address childToken, uint256 amount)`: Mint a particular amount of tokens on Polygon
- `withdraw(address childToken, uint256 amount)`: To burn tokens on the child chain in order to withdraw on the root chain

### Steps for minting tokens on Polygon[#](https://docs.matic.network/docs/develop/l1-l2-communication/fx-portal#steps-for-minting-tokens-on-polygon)

1. Call the `deployChildToken()` on **FxMintableERC20ChildTunnel** and pass the necessary token info as parameters. This emits a TokenMapped event which contains the rootToken and childToken addresses. Note these addresses.
2. Call `mintToken()` on **FxMintableERC20ChildTunnel** to mint tokens on the child chain.
3. Call withdraw() on **FxMintableERC20ChildTunnel** to withdraw tokens from Polygon. Note the transaction hash as this will come in handy to generate the burn proof.
4. Wait for the burn transaction to be included in the checkpoint (~30-45 minutes). After this, generate the burn proof using an example script [here](https://www.notion.so/62c4503d9a6a4bc57c491ee09376d71a).

### Steps for withdrawing tokens on Ethereum[#](https://docs.matic.network/docs/develop/l1-l2-communication/fx-portal#steps-for-withdrawing-tokens-on-ethereum)

Feed the generated burn proof as the argument to `receiveMessage()` in **FxMintableERC20RootTunnel**. After this, the token balance would be reflected on the root chain.

### Steps to deposit tokens back from Ethereum to Polygon[#](https://docs.matic.network/docs/develop/l1-l2-communication/fx-portal#steps-to-deposit-tokens-back-from-ethereum-to-polygon)

1. Make sure you approve **FxMintableERC20RootTunnel** to transfer your tokens.
2. Call `deposit()` in **FxMintableERC20RootTunnel** with the **rootToken** as address of root token and user as the recipient.
3. Wait for the state sync event (~10-15 mins). After this, you can query the target recipient's balance on the child chain.