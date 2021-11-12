---
id: delegator-faq
title: Delegator FAQ
description: Common questions on the delegator operations on the Polygon network.
keywords:
  - docs
  - matic
  - polygon
  - delegator
  - faq
image: https://matic.network/banners/matic-network-16x9.png 
---
import useBaseUrl from '@docusaurus/useBaseUrl';

## What is the staking dashboard URL?

The staking dashboard URL is: https://wallet.polygon.technology/staking

## What is the minimum stake amount?

There is no minimum stake amount to delegate.

## How many rewards will I get if I delegate?

Please use the [staking rewards calculator](https://wallet.polygon.technology/staking/rewards-calculator) to determine your estimates.

## Why does my transaction take so long?

All staking transactions of Polygon happen on the Ethereum mainnet for security reasons.

The time taken to complete a transaction depends on:

* The gas fees that you have allowed.
* The network congestion of the Ethereum mainnet.

 You can always use the **Speed Up** option to increase the gas fees so that your transaction can be completed soon.

## Which wallets are currently supported?

Currently, the MetaMask extension on the desktop browser and Coinbase Wallet are supported. Additionally, you can use WalletConnect and WalletLink from supported mobile wallets to interact with the staking dashboard on desktop/laptop. We will be gradually adding support for other wallets soon.

## Are hardware wallets supported?

Yes, hardware wallets are supported. You can use the **Connect Hardware Wallet** option on MetaMask to connect your hardware wallet and continue the delegation process.

## Can I stake directly on Binance?

Yes, Binance suppports staking the MATIC tokens.

## I have completed my delegation, where can I check details?

Once you have completed your delegation, wait for 12 block confirmations on the Ethereum mainnet (~3-5 minutes). After 12 block confirmations, click [My Account](https://wallet.polygon.technology/staking/my-account) on the dashboard.

## Where can I check my rewards?

Click [My Account](https://wallet.polygon.technology/staking/my-account) on the dashboard.

Check the **Reward** field under your delegated validator.

## Do I need ETH to pay for gas fees?

Yes. You should provision for ~0.05-0.1 ETH to be safe.

## Do I need to deposit MATIC tokens to the Polygon mainnet network for staking?

No. All your funds need to be on the Ethereum mainnet.

## When I try to do the transaction, my **Confirm** button is disabled, why so?

Please check if you have enough ETH for the gas fees.

## When does reward get distributed?

The rewards are distributed whenever a [checkpoint](/docs/validate/glossary#checkpoint-transaction) is submitted.

Currently 107163 Matic tokens are distributed proportionately on each successful checkpoint submission to each delegator based on their stake relative to the overall staking pool of all validators and delegators. Also, the percentage for the reward distributed to each delegator will vary with each checkpoint depending on the relative stake of the delegator, validator and the overall stake.

**Note:** there is a 10% proposer bonus that accrues to the validator who submits the checkpoint, but over time, the effect of the extra bonus is nullified over multiple checkpoints by different validators.

You can track checkpoints on the staking contract [on Etherscan](https://etherscan.io/address/0x86e4dc95c7fbdbf52e33d563bbdb00823894c287).

## Why does reward keep getting decreased every checkpoint?

Actual rewards earned will depend on the actual total locked supply in the network at each checkpoint. This is expected to vary significantly as more MATIC tokens get locked in the staking contracts.

To begin with, rewards will be higher and will keep decreasing as the locked supply % goes up. This change in locked supply is captured at every checkpoint, and rewards are calculated based on this.

## How can I claim my rewards?

1. Click [My Account](https://wallet.polygon.technology/staking/my-account) on the dashboard.
1. Click **Withdraw Reward** under your delegated validator.

## What is the unbonding period?

The unbonding period on Polygon is approximately 9 days now. It was 19 days previously. This period applies to the originally delegated amount and re-delegated amounts — it does not apply to any rewards that were nor re-delegated.

## Will I keep receiving rewards after I unbond?

No. You stop receiving rewards once you unbond.

## How many transactions does the delegation require?

Delegation requires 2 transactions one after the other — one Approve and another Deposit.

## What does Restake Reward mean?

Restaking your rewards means increasing your stake by restaking the rewards you have accumulated.

## Can I stake to any validator?

Yes, you can restake to any validator.

## Which browser is compatible with the staking dashboard?

Chrome, Firefox, and Brave.

## My MetaMask is stuck at confirming after login, what do I do? Or nothing happens when I try to login?

Check for the following:

* If you are using Brave, turn off the option for **Use Crypto Wallets** in the settings panel.
* Check if you are logged into MetaMask.
* Check if you are logged into MetaMask with Trezor/Ledger. Additionally, you need to turn on the permission to call contracts on your Ledger device, if it is not enabled already.
* Check your system timestamp. If the system time is not correct, you need to correct it.

## How do I send funds from Binance or other exchanges to Polygon wallet?

Technically, the Polygon web wallet/staking interface is just a web application. Currently, it supports the following wallets — MetaMask. WalletConnect and WalletLink.

So first you must withdraw your funds from Binance or any other exchange to your Ethereum address on MetaMask. If you don't know how to use MetaMask, Google it a bit. There are plenty of videos and blogs to get started with it.

## If I have earned rewards while delegating, and if I add additional funds to the same validator node, what happens?

If you have not re-delegated your rewards before delegating additional funds to the same validator node, your rewards will be withdrawn automatically.

In case you do not want that to happen, re-delegate your rewards before delegating additional funds.

## I have delegated my tokens via MetaMask on the staking dashboard. Do I need to keep my system or device on?

No. Once your delegation transactions are confirmed, and you can see your tokens reflected in the **Your Stake** and **Reward** fields in [My Account](https://wallet.polygon.technology/staking/my-account), then you are done. There is no need to keep your system or device on.

## I have unbonded. How long will it take to unbond?

The unbonding period is currently set to 80 checkpoints. Every checkpoint takes approximately 34 minutes. However, some checkpoints could be delayed upto ~1 hour due to congestion on the Ethereum mainnet.

## Do I know when the unbonding period is over?

Yes, you can check status of your current unbonding period on the [History](https://wallet.polygon.technology/staking/history) tab.

Every checkpoint takes approximately 30 minutes. However, some checkpoints could be delayed upto ~1 hour due to congestion on the Ethereum mainnet.

## How do I switch my delegation from Foundation nodes to external nodes?

You can switch your Delegation using the **Move Stake** option on the [staking dashboard](https://wallet.polygon.technology/staking/my-account). This will switch your delegation from the Foundation node to any other external node of your choice.

## Will there be any ubonding period when I switch delegation from Foundation nodes to external nodes?

There will be no unbonding period when you switch delegation from Foundation nodes to external nodes. It will be a direct switch without any delays. However, if you are unbonding from a Foundation node or an external node, there will be an unbonding period for that.

## Are they any specifics to choose an external node during switch delegation?

No. You can choose any node of your choice.

## What happens to my rewards that are accumalated if I switch delegation from Foundation to external node?

If you haven't already withdrawn your rewards before switching delegation, then upon successfull switch of your delegation from Foundation to external node, the rewards that were accumalated until then will be transferred back to your account.

## Will delegation on the external nodes work the same as Foundation nodes?

Yes, it will work the same as Foundation nodes.

## Will I still get rewards after delegating to an external node?

Yes, rewards will be distributed the same as earlier with the Foundation nodes. Every successful submission of a [checkpoint]((/docs/validate/glossary#checkpoint-transaction)) will yield rewards. Rewards will be distributed and calculated at every checkpoint relative to the stake ratio, as currently implemented.

## Will there be any unbonding period if I unbond from an external node?

Yes, the unbonding period will stay the same as currently implemented. 80 checkpoints.

## Will there be any locking period after I switch my delegation from Foundation to external node?

No. There won't be any locking period after you switch your delegation.

## Can I partially switch my delegation from Foundation to external nodes?

Yes, you will have the option to partially move your stake from Foundation node to an external node. The remaining partial stake will remain on the Foundation node. You can then move that to another node of your choice or the same node.

## Can I switch delegation from an external node to another external node?

The **Move Stake** option is available to switch the delegation between any nodes. The only exception is moving stake from one Foundation node to another Foundation node is not allowed.

## How many transactions do I need to pay for gas when I do a Move Stake?

The Move Stake is a single transaction only. All transactions would be on the Ethereum mainnet, so you would need to spend some ETH while doing the Move Stake transaction.

## If the external validator is missing signing checkpoints, does that mean I lose on rewards too?

Yes. Rewards are distributed every [checkpoint](/docs/validate/glossary#checkpoint-transaction) and each validator is supposed to sign the checkpoint transaction. However, if any validator misses signing the checkpoint transaction for that checkpoint, rewards are not issued for that validator. Which in return means that you as delegator will not earn any rewards for that particular checkpoint.
