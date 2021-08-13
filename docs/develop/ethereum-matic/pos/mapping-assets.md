---
id: mapping-assets
title: Mapping Assets using POS
description: Build your next blockchain app on Matic.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png
---

## Quick Summary:

This section deals with a very necessary prerequisite to using the Polygon bridge - Mapping. Mapping is the process of creating and maintaining a stable connection between the contracts on the two networks(Ethereum and Polygon) and transferring assets to and fro the two networks.

## Introduction

 To understand how mapping works, the first thing to do is to understand the technical terms used to describe the transactions. 

- The **Root Chain** here refers to either the Goerli or Ethereum Mainnet
- The **Child Chain** refers to either Mumbai or Polygon Mainnet

If you have your token contract deployed on the **Root Chain** and want to move it to the **Child Chain** then this is the documentation for you. If however your intent is to deploy your contract on the **Polygon** mainnet, mint the tokens on the **Child Chain** and then move them back to the **Root Chain** - ****the end result of this is what we call Polygon Mintable Assets -  then you need to follow this [guide](https://docs.matic.network/docs/develop/ethereum-matic/mintable-assets/)

## Standard Child Token

If your use case is a very standard ERC20/ERC721/ERC1155 contract, then you're in luck. Polygon makes its very easy for you to simply submit a mapping request at [https://mapper.matic.today/](https://mapper.matic.today/). Do that right now and the team will be on standby to automatically deploy a standard child token contract for you.

Incase you're not sure what a Standard Child Token contract looks like, please check the following links to see if they bear resemblance to your usecase

1. [ERC20](https://github.com/maticnetwork/pos-portal/blob/master/flat/ChildERC20.sol#L1492-#L1508)
2. [ERC721](https://github.com/maticnetwork/pos-portal/blob/master/flat/ChildERC721.sol#L2157-#L2238)
3. [ERC1155](https://github.com/maticnetwork/pos-portal/blob/master/flat/ChildERC1155.sol#L1784-#L1818)

Please visit this [link](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request/) to understand how to create a new mapping request.

## Custom Child Token

There are many reasons why you may need a custom child token (with additional functions) rather than standard child token. To do this, you'll have to deploy your token contracts on the Child Chain and then submit your mapping request [here](https://mapper.matic.today/). Make sure to include the address of your deployed child token contract and you should be good to go. Let's describe an example of creating a custom child token contract.

**Your custom child contract should follow certain guidelines before you deploy it on the child chain.**

The custom child contract must have: 

A **deposit** method**.** The **deposit** method should be present in your custom child contract. This function is called by the ChildChainManagerProxy contract whenever a deposit is initiated from the root chain. This deposit function internally mints the token on the child chain.

A **withdraw** method. The **withdraw** method must be available as it is what is called to burn your tokens on the child chain. Burning is the first step of the withdrawal process. These rules need to be followed to maintain a proper balance of assets between the two child and root chains.

**Sidenote**: No token minting in constructor of child token contract.

### Implementation

Now that we covered why we need to implement **deposit** & **withdraw** methods in child token contract, we can now proceed for implementing it.

```jsx
// ChildERC20.sol

pragma solidity 0.6.6;

import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/math/SafeMath.sol";

contract ChildERC20 is ERC20,
{
    using SafeMath for uint256;

    constructor(string memory name, string memory symbol, uint8 decimals) public ERC20(name, symbol) {
        
        _setupDecimals(decimals);
        // can't mint here, because minting in child chain smart contract's constructor not allowed
        // _mint(msg.sender, 10 ** 27);
    
    }

    function deposit(address user, bytes calldata depositData) external {
        uint256 amount = abi.decode(depositData, (uint256));

        // `amount` token getting minted here & equal amount got locked in RootChainManager
        _totalSupply = _totalSupply.add(amount);
        _balances[user] = _balances[user].add(amount);
        
        emit Transfer(address(0), user, amount);
    }

    function withdraw(uint256 amount) external {
        _balances[msg.sender] = _balances[msg.sender].sub(amount, "ERC20: burn amount exceeds balance");
        _totalSupply = _totalSupply.sub(amount);
        
        emit Transfer(msg.sender, address(0), amount);
    }

}
```

Look into the code block and you'll notice that the deposit function can be called by anyone, which shouldn't be really. In order to prevent this, we're going to make sure it can only be called by ChildChainManagerProxy. (ChildChainManagerProxy - on Mumbai , on Polygon Mainnet)

```jsx
// ChildERC20.sol

pragma solidity 0.6.6;

import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/math/SafeMath.sol";

contract ChildERC20 is ERC20,
{
    using SafeMath for uint256;
    // keeping it for checking, whether deposit being called by valid address or not
    address public childChainManagerProxy;
    address deployer;

    constructor(string memory name, string memory symbol, uint8 decimals, address _childChainManagerProxy) public ERC20(name, symbol) {
        
        _setupDecimals(decimals);
        childChainManagerProxy = _childChainManagerProxy;
        deployer = msg.sender;

        // Can't mint here, because minting in child chain smart contract's constructor not allowed
        //
        // In case of mintable tokens it can be done, there can be external mintable function too
        // which can be called by some trusted parties
        // _mint(msg.sender, 10 ** 27);
    
    }

    // being proxified smart contract, most probably childChainManagerProxy contract's address
    // is not going to change ever, but still, lets keep it 
    function updateChildChainManager(address newChildChainManagerProxy) external {
        require(newChildChainManagerProxy != address(0), "Bad ChildChainManagerProxy address");
        require(msg.sender == deployer, "You're not allowed");

        childChainManagerProxy = newChildChainManagerProxy;
    }

    function deposit(address user, bytes calldata depositData) external {
        require(msg.sender == childChainManagerProxy, "You're not allowed to deposit");

        uint256 amount = abi.decode(depositData, (uint256));

        // `amount` token getting minted here & equal amount got locked in RootChainManager
        _totalSupply = _totalSupply.add(amount);
        _balances[user] = _balances[user].add(amount);
        
        emit Transfer(address(0), user, amount);
    }

    function withdraw(uint256 amount) external {
        _balances[msg.sender] = _balances[msg.sender].sub(amount, "ERC20: burn amount exceeds balance");
        _totalSupply = _totalSupply.sub(amount);
        
        emit Transfer(msg.sender, address(0), amount);
    }

}
```

This updated implementation can be used for mapping.

Steps :

1. Deploy root token on root chain i.e. {Goerli, Ethereum Mainnet}
2. Ensure your child token has the **deposit** & **withdraw** functions.
3. Deploy the child token on child chain i.e. {Polygon Mumbai, Polygon Mainnet}
4. Submit a mapping request, to be resolved by team.

### Request Submission

Please go use this [link](https://docs.matic.network/docs/develop/ethereum-matic/submit-mapping-request) to submit a mapping request.