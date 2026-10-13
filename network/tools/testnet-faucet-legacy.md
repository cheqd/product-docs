---
description: >-
  Archived information about the legacy standalone cheqd testnet faucet, which
  has been replaced by the faucet in cheqd Studio.
---

# Legacy Testnet Faucet (archived)

{% hint style="warning" %}
The standalone testnet faucet at `testnet-faucet.cheqd.io` is no longer in use. To request testnet CHEQ tokens, log in to [cheqd Studio](https://studio.cheqd.net/) and go to **Account** > **Balances & Faucet**. See [Testnet Faucet](testnet-faucet.md) for details.

This page is kept for reference only.
{% endhint %}

> 🛠️ **Github repository**: [cheqd/faucet-ui](https://github.com/cheqd/faucet-ui)

The legacy cheqd testnet faucet was a self-serve site that allowed app developers and node operators who wanted to try out our identity functionality or node operations to request test CHEQ tokens, without having to spend money to acquire “real” CHEQ tokens on mainnet.

We built this using [Cloudflare Pages](https://developers.cloudflare.com/pages/) as it provides a fast way to create serverless applications which are able to scale up and down dynamically depending on traffic, especially for something such as a testnet faucet which may not receive consistent levels of traffic. The backend for this faucet works using an existing [CosmJS faucet app](https://github.com/cosmos/cosmjs/tree/main/packages/faucet) to handle requests, run using a Digital Ocean app wrapped in a Dockerfile.

## **Why was this valuable?**

This solution:

1. Helped to keep the team focused on building, as no longer did we need to dedicate time for manually responding to requests for tokens.
2. Created a far more cost effective way of handling testnet token distributions
3. Could be utilised by developers to test cheqd functionality far more efficiently
4. Could be used by other Cosmos projects to reduce operational overheads and reduce headaches around distributing testnet tokens
