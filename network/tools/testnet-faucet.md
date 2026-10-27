---
description: >-
  Request testnet CHEQ for any cheqd testnet address from cheqd Studio, in the
  portal or through the API.
---

# Testnet Faucet

The cheqd testnet faucet gives app developers and node operators free testnet CHEQ, so they can try cheqd's identity functionality without buying mainnet CHEQ.

The faucet is part of [cheqd Studio](https://studio.cheqd.net/). It can fund **any cheqd testnet address**, including a wallet you created yourself for the cheqd SDK, Veramo, Credo or ACA-Py, not only the address tied to your Studio account.

## Before you start

* **A cheqd Studio account.** [Sign up for cheqd Studio](https://studio.cheqd.net/). New accounts start on a 30-day trial of the Basic plan, with no card needed.
* **An active Basic or Build plan, or that trial.** Without one, faucet requests return `403`.
* **A cheqd testnet address** (`cheqd1…`) if you want to fund your own wallet. See [Setup the cheqd SDK](../../advanced/sdk/setup-the-cheqd-sdk.md), or set up a [Keplr](../wallets/keplr-setup.md) wallet.

## Request tokens in cheqd Studio

1. Log in to [cheqd Studio](https://studio.cheqd.net/).
2. In the left sidebar, open the **Account** tab and select **Balances & Faucet**.
3. The address is filled in with your Studio testnet account. Replace it with your own `cheqd1…` address if you want to fund that instead.
4. Choose an amount, or leave the suggested one, and request the tokens.

## Request tokens through the API

Call `POST /account/faucet` with an API key from your Studio account (see [API keys](../../getting-started/studio/api-keys.md)):

```bash
curl -X POST https://studio-api.cheqd.net/account/faucet \
  -H "x-api-key: $STUDIO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"address": "cheqd1...", "amount": 10000}'
```

Both fields are optional:

* `address`: the cheqd testnet address to fund. If you leave it out, your Studio testnet account is funded.
* `amount`: in CHEQ. If you leave it out, 10,000 CHEQ is sent, or less if a limit below leaves less room.

The response shows the amount sent and the quota you have left.

## Limits

| Limit | Value |
| --- | --- |
| Testnet CHEQ per Studio account per calendar month (UTC), across all addresses | 100,000 |
| Most an address can hold for the faucet to top it up | 100,000 |
| Amount sent when none is requested | 10,000 |
| Time between requests from one account | 10 seconds |

New Studio accounts start with no testnet CHEQ. Nothing is sent automatically, because you may want to fund a wallet of your own instead. The Studio dashboard tells you when your testnet account is empty and links to **Balances & Faucet**.

## Errors

| Status | Meaning |
| --- | --- |
| `400` | The address is not a valid cheqd address, or the amount is not a positive number |
| `401` | No valid login or API key |
| `403` | No active Basic or Build plan or trial, or the plan does not include the faucet |
| `429` | Monthly quota used up, or a request too soon after the last one. The `Retry-After` header gives the seconds to wait |
| `502` or `504` | The faucet did not answer. A `504` request may still have been processed, so check the balance before retrying |

The full reference is in the [cheqd Studio API documentation](https://studio-api.cheqd.net/swagger/).

## Legacy faucet

The testnet faucet used to be a standalone site at `testnet-faucet.cheqd.io`. That site is no longer public. Information about it is kept in the [Legacy Testnet Faucet (archived)](testnet-faucet-legacy.md) page.
