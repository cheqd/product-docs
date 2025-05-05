---
description: >-
  Walt ID Community Stack with cheqd support for Decentralized Identifiers
  (DIDs), DID-Linked Resources (DLRs) and Credentials.
cover: ../.gitbook/assets/walt.id logo.png
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

# 🫐 Walt.id Community Stack

Community Stack[ is an Open Source SDK from walt.id](https://walt.id/ssi-kit) which supports cheqd functionality, and other identity networks, via REST API calls or through a native CLI tool. &#x20;

## Key Features

Community Stack may be the right choice, with the following benefits

* **Open Source & Extensible**\
  Completely open-source and built for developer flexibility. Run it yourself or extend to meet your unique requirements.
* **cheqd Integration**\
  Supports `did:cheqd` for DID creation, resolution, credential issuance, and DID-Linked Resources.
* **Multi-ledger Support**\
  Use other DID methods such as `did:key`, `did:web`, `did:ebsi`, and more—across public and private networks.
* **API & CLI Interfaces**\
  Interact with the stack through easy-to-use REST APIs or a powerful CLI tool.
* **Credential Format Support**\
  Issue and verify credentials using:
  * JSON-LD (W3C Verifiable Credentials)
  * JWT-based credentials (W3C Verifiable Credentials)
  * SD-JWT-VC
  * mDL

***

### Ideal for Developers Who

* Want to experiment with identity use cases using `did:cheqd`
* Need to run identity infrastructure locally or in private cloud
* Prefer open tooling over closed-source SaaS platforms
* Are building apps that need to issue, verify, or present VCs in a standards-based way

***

### Looking for Managed Hosting?

In addition to the Community Stack, walt.id also offers a range of **SaaS identity services** for customers who need:

* Turnkey deployment
* Enterprise-grade reliability
* SLA-backed support
* Deep integration with wallet apps and verifiers

Learn more about walt.id’s commercial offerings at [walt.id](https://walt.id).

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create a fully conformant DID using Community Stack.</td><td><a href="https://docs.walt.id/v/ssikit/ecosystems/cheqd/create-did">https://docs.walt.id/v/ssikit/ecosystems/cheqd/create-did</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Credential</strong></mark></td><td>Issue a fully W3C conformant Credential using <strong>walt.id CLI or over REST API.</strong></td><td><a href="https://docs.walt.id/v/ssikit/ecosystems/cheqd/issue-vc">https://docs.walt.id/v/ssikit/ecosystems/cheqd/issue-vc</a></td></tr><tr><td><mark style="color:blue;"><strong>Verify a Credential</strong></mark></td><td>Verify a fully W3C conformant Credential using SSI Kit.</td><td><a href="https://docs.walt.id/v/ssikit/ecosystems/cheqd/verify-vc">https://docs.walt.id/v/ssikit/ecosystems/cheqd/verify-vc</a></td></tr></tbody></table>

## Architecture

In order to be able to onboard the `did:cheqd` on testnet and mainnet, Community Stack relies on a [cheqd universal registrar](https://github.com/cheqd/did-registrar) deployed on walt.id infrastructure. The DID will be created using a key imported into or also created with Community Stack.

<figure><img src="../.gitbook/assets/walt id architecture.png" alt=""><figcaption><p>Walt.id and cheqd architecture diagram</p></figcaption></figure>

## Supported Credential types

Community Stack natively supports the following Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>SD-JWT</strong></mark></td><td>Selective Disclosure JWT (SD-JWT) is the most <strong>commonly adopted credential format for European Digital Identity Ecosystems</strong>, allowing users to selectively disclose which attributes they would like to share in a presentation.</td><td></td></tr><tr><td><mark style="color:blue;"><strong>JSON (JWT)</strong></mark></td><td><strong>JSON Web Token (JWT)</strong> Credentials are a simple way to transmit Trusted Data as a JSON object.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld</a></td></tr></tbody></table>
