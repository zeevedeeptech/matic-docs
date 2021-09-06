---
id: widget
title: Wallet Widget
sidebar_label: Wallet Widget
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - matic
image: https://matic.network/banners/matic-network-16x9.png 
---
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

Wallet widget is a UI tool which can be embedded in any web application for executing bridge transactions - Deposit & Withdraw. 

Every widget is identified by an unique name which you can get from [Widget dashboard](https://wallet.polygon.technology/widget-dashboard) .

### Widget dashboard

Widget can be created from the widget dashboard page in the wallet application. It allows the user to create a new widget with some customisable options.

Once the widget is created, You can copy code snippet and add it in your application or use the widget name and configure by yourself.

Here is link to widget dashboard - https://wallet.polygon.technology/widget-dashboard

## Install

Widget is exported as javascript library and available as npm package. 

```bash 
npm i @maticnetwork/wallet-widget
```

## Examples

We have created examples for different framework and tools to help you with the development. All examples are present at - [https://github.com/maticnetwork/wallet-widget-example](https://github.com/maticnetwork/wallet-widget-example)

## How to use

Create instance of widget with some configuration

```javascript
import { Widget } from "@maticnetwork/wallet-widget";

var widget = new Widget({
    appName: "<widget name>", //widget name from dashboard
    target: '#btnMaticWidget', // element selector for showing widget on click
    network: 'testnet' // network to be used - testnet or mainnet
});
```

Create widget whenever you are ready. It is best to call create function after document is loaded.

```javascript 
widget.create();
```

## Configuration

Configuration can be supplied in Widget constructor.

## Available configuration are

- **target** : string - CSS selector for showing widget on click of element. For example, "#btnMaticWidget" will be the target in the code below.

```javascript
<button id="btnMaticWidget">Matic widget</button>
```

- **network** : string - network to be used. Two options are available - 'testnet' or 'mainnet'.
- **width** : number - Width of the widget
- **height** : number - Height of the widget
- **autoShowTime** : number - Auto show widget after specified time in millisecond
- **appName** : string - name of your app, this can be retrieved on widget dashboard.
- **position** : string - Sets the position of the widget. The available options are -
    - center
    - bottom-right
    - bottom-left
- **amount** : string - Prefill the amount in text box
- **page** : string - select the page. Available options are - `withdraw`, `deposit`.
- **overlay** : boolean - show overlay when widget is opened. By default it is false.
- **style** : object - apply some css styles to the widget. 

```
var widget = new MaticWidget({
    appName: "<your app id>", //appName from dashboard
    target: '#btnMaticWidget', // element selector for showing widget on click
    network: 'testnet' // network to be used - testnet or mainnet,
    style:{
      color:'red'
    }
});
```

## Events

Widget emits some events which can be used to know what is happening inside the application.

### Subscribe to events

```javascript
widget.on('load',()=>{
  console.log('widget is loaded');
})
```

### Unsubscribe to events

```javascript 
widget.off('load',<callback>)
```

> Callback should be same as what was used to subscribe the event. So its better to store the callback in a variable.`

## List of events:

- **load** - Widget is loaded
- **close** - Widget is closed
- **approveInit** - Approval transaction is initialized
- **approveComplete** - Approval transaction is completed
- **approveError** - Approval transaction failed due to some error, or the user denied the transaction on Metamask
- **depositInit** - Deposit transaction is initialized
- **depositComplete** - Deposit transaction is completed
- **depositError** - Deposit transaction failed due to some error, or the user denied the deposit complete transaction on Metamask
- **burnInit** - Withdrawal burn transaction is initialized
- **burnComplete** - Withdrawal burn transaction is completed
- **confirmWithdrawInit** - Withdrawal is checkpointed and confirm transaction is initialized
- **confirmWithdrawComplete** - Withdrawal confirm transaction in completed
- **confirmWithdrawError** - Withdrawal confirm transaction failed due to some error, or the user denied the withdrawal confirm transaction on Metamask
- **exitInit** - Withdrawal exit transaction is initialized
- **exitComplete** - Withdrawal exit transaction is completed
- **exitError** - Withdrawal exit transaction failed due to some error, or the user denied the withdrawal exit transaction on Metamask

## APIS

- **show** - 
show the widget

```javascript
widget.show()
```

- **hide** - 
hide the widget

```javascript
widget.hide()
```

- **on** - 
subscribe to events

```javascript
widget.on('<event name>', callback)
```

- **off** - 
unsubscribe to events

```javascript
widget.off('<event name>', callback)
```
