---
description: >-
  Use the testnet faucet to top up your testnet wallet and start using cheqd's
  identity functionality.
---

# Testnet Faucet

> 🛠️ **Github repository**: [cheqd/faucet-ui](https://github.com/cheqd/faucet-ui)

The [cheqd testnet faucet](https://testnet-faucet.cheqd.io/) is a self-serve site that allows app developers and node operators who want to try out our identity functionality or node operations to request test CHEQ tokens, without having to spend money to acquire “real” CHEQ tokens on mainnet.

We built this using [Cloudflare Pages](https://developers.cloudflare.com/pages/) as it provides a fast way to create serverless applications which are able to scale up and down dynamically depending on traffic, especially for something such as a testnet faucet which may not receive consistent levels of traffic. The backend for this faucet works using an existing [CosmJS faucet app](https://github.com/cosmos/cosmjs/tree/main/packages/faucet) to handle requests, run using a Digital Ocean app wrapped in a Dockerfile.

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Testnet Faucet</strong></mark></td><td>Click here to add test CHEQ tokens to your cheqd address and account. </td><td><a href="https://testnet-faucet.cheqd.io/">https://testnet-faucet.cheqd.io/</a></td></tr></tbody></table>

## **Why is this valuable?**

This solution:

1. Helps to keep the team focused on building, as no longer do we need to dedicate time for manually responding to requests for tokens.
2. Creates a far more cost effective way of handling tesnet token distributions
3. Can be utilised by developers to test cheqd functionality far more efficiently
4. Can be used by other Cosmos projects to reduce operational overheads and reduce headaches around distributing testnet tokens
