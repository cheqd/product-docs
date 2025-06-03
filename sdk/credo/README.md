---
description: >-
  Credo SDK with cheqd support for Decentralized Identifiers (DIDs), DID-Linked
  Resources (DLRs) and Credentials.
cover: ../../.gitbook/assets/Credo.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🍏 Credo

## Get Started with Credo

**Credo** is a TypeScript-based framework for building Self-Sovereign Identity (SSI) agents and services. It is designed for **modularity**, **interoperability**, and **compliance** with the European Architecture and Reference Framework (ARF).

Previously known as _Aries Framework JavaScript_, Credo has recently been **rebranded** and transitioned from the **Hyperledger Foundation** to the **Open Wallet Foundation**, reflecting its broader mission and growing community.

Credo has evolved into a more flexible and general-purpose SSI framework. A major milestone in this evolution is the full **integration with** [**cheqd**](https://cheqd.io) — making Credo the first framework to support **AnonCreds on non-Indy ledgers**.

This integration showcases Credo’s expanding support for:

* Multiple **credential formats**
* Diverse **DID methods**
* Interoperable **ledger technologies**

Using Credo, developers can now issue **Verifiable Credentials**, create **Credential Schemas**, and define **Credential Definitions** using **cheqd-native AnonCreds**, enabled by the **cheqd AnonCreds Object Method**.

## Key Features

Credo may be the right choice as it has the following benefits:

* **TypeScript-native** — strongly typed, developer-friendly, and production-ready
* **Modular and extensible** — use only what you need, plug in new components
* **Cross-ledger compatibility** — supports Indy, cheqd, and beyond
* **Flexible credential support** — AnonCreds, W3C JSON-LD, JWT
* **Aligned with EU standards** — interoperable with EU ARF and continuing to align with eIDAS 2.0 implementing acts
* **Backed by the Open Wallet Foundation** — with growing community adoption

### Breaking New Ground: Credo + cheqd

Credo is now the **first framework to support AnonCreds on non-Indy ledgers**, thanks to a deep integration with [**cheqd**](https://cheqd.io). ACA-Py has since followed suit.

This includes:

* Issuance of Verifiable Credentials using cheqd DIDs
* Creation of Credential Schemas and Credential Definitions
* Support for the **cheqd AnonCreds Object Method**
* Native DID and resource support for `did:cheqd`

## Get started

Get setup with your Credo agent and begin using cheqd's functionality below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup your Credo Agent</strong></mark></td><td>Setup the cheqd agent to start being able to use cheqd inside Credo.</td><td><a href="set-up-agent.md">set-up-agent.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create a Decentralized Identifier (DID)</strong></mark></td><td>Use the cheqd DID module to start creating cheqd DIDs.</td><td><a href="dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resources (DLRs)</strong></mark></td><td>Create generic DID-Linked Resources, as well as schemas and credential definitions.</td><td><a href="did-linked-resources/">did-linked-resources</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue and present Verifiable Credentials using a cheqd DID.</td><td><a href="credentials/anoncreds/issue-credential.md">issue-credential.md</a></td></tr></tbody></table>



## Supported Credential types

Credo supports the following Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>AnonCreds</strong></mark></td><td><strong>AnonCreds</strong> is a Zero-Knowledge Credential format, offering full Holder privacy and selective disclosure.</td><td><a href="https://hyperledger.github.io/anoncreds-spec/">https://hyperledger.github.io/anoncreds-spec/</a></td></tr><tr><td><mark style="color:blue;"><strong>SD-JWT</strong></mark></td><td>Selective Disclosure JWT (SD-JWT) is the most <strong>commonly adopted credential format for European Digital Identity Ecosystems</strong>, allowing users to selectively disclose which attributes they would like to share in a presentation.</td><td><a href="https://docs.cheqd.io/product/learning-docs/decentralised-identity/credentials/types/sd-jwt">https://docs.cheqd.io/product/learning-docs/decentralised-identity/credentials/types/sd-jwt</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/decentralised-identity/credentials/types/json-ld">https://learn.cheqd.io/decentralised-identity/credentials/types/json-ld</a></td></tr></tbody></table>

## Watch our demo video

{% embed url="https://www.youtube.com/watch?v=8ERjaB6iP48" %}
cheqd and Animo AnonCreds demo
{% endembed %}

## Enterprise Support for Credo

Below are a list of enterprise applications that leverage Credo under the hood:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Paradym (Animo Solutions)</strong></mark></td><td>Paradym by Animo Solutions fully supports <strong>EU Architecture &#x26; Reference Framework</strong> standards with cheqd under the hood.</td><td><a href="../../.gitbook/assets/paradym-for-website.png">paradym-for-website.png</a></td><td><a href="https://paradym.id/">https://paradym.id/</a></td></tr><tr><td><mark style="color:blue;"><strong>Hovi Studio (Hovi)</strong></mark></td><td>Hovi provides an all-in-one platform for issuing and managing Verifiable Credentials with cheqd DIDs supported.</td><td><a href="../../.gitbook/assets/Hovi.png">Hovi.png</a></td><td><a href="https://www.hovi.id/">https://www.hovi.id/</a></td></tr><tr><td><mark style="color:blue;"><strong>Sudo Platform (Anonyome Labs)</strong></mark></td><td>API-first developer platform for cheqd DIDs and support for multiple Verifiable Credential formats.</td><td><a href="../../.gitbook/assets/Anonyome Labs.png">Anonyome Labs.png</a></td><td><a href="https://anonyome.com/businesses/decentralized-identity-for-developers/">https://anonyome.com/businesses/decentralized-identity-for-developers/</a></td></tr></tbody></table>
