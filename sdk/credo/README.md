---
description: >-
  Credo, formerly Hyperledger Aries Framework JavaScript (AFJ), with cheqd
  support
---

# 🍏 Credo

[Credo](https://credo.js.org/) is a framework written in TypeScript for building **SSI Agents and services** that aims to be **compliant and interoperable** with the standards defined in the European Architecture and Reference Framework.

The Aries Framework JavaScript project has recently been rebranded to "Credo" and was moved from the Hyperledger Foundation to the [Open Wallet Foundation](https://openwallet.foundation/).&#x20;

In the recent months, Credo has heavily been expanding into a more modular and "less specific" framework. The integration with cheqd is a prime example of this, being the first true showcase of anchoring AnonCreds on non-Indy ledgers. Supporting more credential formats, ledgers and DID methods is crucial and essential to the continual development of Credo. cheqd is now fully supported in Credo and can be used for the issuance of Verifiable Credentials as well as the creation of Credential Schemas and Credential Definitions.

AnonCreds using cheqd are facilitated using the [cheqd AnonCreds Object Method](../../advanced/anoncreds/).

## Architecture

<figure><img src="../../.gitbook/assets/afj packages diagram.png" alt=""><figcaption><p>Aries Framework JavaScript packages diagram</p></figcaption></figure>

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Agent Setup</strong></mark></td><td>Setup the cheqd agent to start being able to use cheqd inside Credo.</td><td><a href="setup-credo-agent.md">setup-credo-agent.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create cheqd DIDs</strong></mark></td><td>Use the cheqd DID module to start being able to create cheqd DIDs.</td><td><a href="decentralized-identifiers-dids/create-a-did.md">create-a-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Credential</strong></mark></td><td>Issue a digital Credential using a cheqd DID.</td><td><a href="verifiable-credentials-and-presentations/issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr></tbody></table>



## Supported Credential types

Aries Framework JavaScript supports the following Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>SD-JWT</strong></mark></td><td>Selective Disclosure JWT (SD-JWT) is the most <strong>commonly adopted credential format for European Digital Identity Ecosystems</strong>, allowing users to selectively disclose which attributes they would like to share in a presentation.</td><td><a href="https://docs.cheqd.io/product/learning-docs/decentralised-identity/credentials/types/sd-jwt">https://docs.cheqd.io/product/learning-docs/decentralised-identity/credentials/types/sd-jwt</a></td></tr><tr><td><mark style="color:blue;"><strong>AnonCreds</strong></mark></td><td><strong>AnonCreds</strong> is a Zero-Knowledge Credential format, offering full Holder privacy and selective disclosure.</td><td><a href="https://hyperledger.github.io/anoncreds-spec/">https://hyperledger.github.io/anoncreds-spec/</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/decentralised-identity/credentials/types/json-ld">https://learn.cheqd.io/decentralised-identity/credentials/types/json-ld</a></td></tr></tbody></table>

## Watch our demo video

{% embed url="https://www.youtube.com/watch?v=8ERjaB6iP48" %}
cheqd and Animo AnonCreds demo
{% endembed %}
