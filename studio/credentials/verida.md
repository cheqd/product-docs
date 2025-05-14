---
description: Setup your Verida Wallet to receive W3C Verifiable Credentials.
---

# Setup Verida Wallet

cheqd Studio currently is setup to work with the Verida Wallet. In 2025, we will be building compatibility with a full suite of other digital identity wallets.&#x20;

## Verida Wallet <a href="#verida-wallet" id="verida-wallet"></a>

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Verida wallet</strong></mark></td><td>Download the Verida wallet to receive credentials issued by cheqd Studio to a <code>did:vda</code> address.</td><td><a href="../../.gitbook/assets/verida wallet.png">verida wallet.png</a></td><td><a href="https://www.verida.network/verida-wallet">https://www.verida.network/verida-wallet</a></td></tr></tbody></table>

### Subject DID

On the "**Profile**" tab, you will see your `did:vda` address under the section "**DID**".

For example: `"did:vda:testnet:0xD7477C4a75143Af16A967381d09650A533Bd0DD7"`

### Rendering credentials

Credentials stored with the [credential schema](https://common.schemas.verida.io/credential/base/v0.2.0/schema.json) will automatically be rendered in the Verida Wallet as a credential. This custom display includes:

1. A scannable QR code
2. The profile icon of the Verida DID that issued / signed the credential
3. A tick of approval indicating the credential has been verified
