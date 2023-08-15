---
description: Hyperledger Aries Framework JavaScript (AFJ) with cheqd support
---

# 🍏 Aries Framework JavaScript

Hyperledger Aries Framework JavaScript is a framework written in TypeScript for building **SSI Agents and DIDComm services** that aims to be **compliant and interoperable** with the standards defined in the [Aries RFCs](https://github.com/hyperledger/aries-rfcs).

In the recent months AFJ has heavily been expanding into a more modular and "less specific" framework. The integration with cheqd is a prime example of this, being the first true showcase of anchoring AnonCreds on non-Indy ledgers Supporting more credential formats, ledgers and DID methods is crucial and essential to the continual development of AFJ. cheqd is now fully supported in AFJ and can be used for the issuance of AnonCreds as well as the creation of Credential Schemas and Credential Definitions.

AnonCreds using cheqd are facilitated using the [cheqd AnonCreds Object Method](../advanced/anoncreds/).

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Agent Setup</strong></mark></td><td>Setup the cheqd agent to start being able to use cheqd inside Aries Framework JavaScript.</td><td><a href="https://aries.js.org/guides/0.4/getting-started/set-up/cheqd">https://aries.js.org/guides/0.4/getting-started/set-up/cheqd</a></td></tr><tr><td><mark style="color:blue;"><strong>Create cheqd DIDs</strong></mark></td><td>Use the cheqd DID module to start being able to create cheqd DIDs.</td><td><a href="https://aries.js.org/guides/0.4/tutorials/cheqd">https://aries.js.org/guides/0.4/tutorials/cheqd</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Credential</strong></mark></td><td>Issue an AnonCreds Credential using a cheqd DID.</td><td><a href="https://aries.js.org/guides/0.4/tutorials/issue-a-credential">https://aries.js.org/guides/0.4/tutorials/issue-a-credential</a></td></tr></tbody></table>

## Architecture

<figure><img src="../.gitbook/assets/afj packages diagram.png" alt=""><figcaption><p>Aries Framework JavaScript packages diagram</p></figcaption></figure>



## Supported Credential types

Aries Framework JavaScript supports the following Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>AnonCreds</strong></mark></td><td><strong>AnonCreds</strong> is a Zero-Knowledge Credential format, offering full hHlder privacy and selective disclosure. </td><td><a href="https://hyperledger.github.io/anoncreds-spec/">https://hyperledger.github.io/anoncreds-spec/</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld</a></td></tr></tbody></table>

## A
