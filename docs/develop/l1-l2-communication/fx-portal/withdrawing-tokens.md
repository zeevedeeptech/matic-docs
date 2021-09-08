---
id: withdrawing-tokens
title: Withdrawing Tokens on the Root Chain
description: Build your next blockchain app on Polygon.
keywords:
  - docs
  - polygon
image: https://matic.network/banners/matic-network-16x9.png
---

# Withdrawing your Tokens on the Root Chain

After you have performed `withdraw()` on the child chain, it will take 30-60 minutes for a checkpoint to happen. Once the next checkpoint includes the burn transaction, you can withdraw the tokens on the root chain.

1. Generate the burn proof using the tx hash and MESSAGE_SENT_EVENT_SIG. An example script to generate the proof can be found [here](https://www.notion.so/62c4503d9a6a4bc57c491ee09376d71a).
2. Feed the generated payload as the argument to `receiveMessage()` in the respective root tunnel contract.