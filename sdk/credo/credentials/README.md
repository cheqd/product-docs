---
description: >-
  Issue Verifiable Credentials with Credo, signed by cheqd Decentralized
  Identifiers (DIDs).
---

# Verifiable Credentials and Presentations

{% hint style="info" %}
:books: **Learn about Verifiable Credentials and Presentations**

If you want to learn more about what [Verifiable Credentials and Presentations are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc)
{% endhint %}

**Credo** provides full support for working with **Verifiable Credentials (VCs)** and **Verifiable Presentations (VPs)**, based on the standards defined by the **W3C** and the **Aries RFCs**.

Credo enables users to **issue**, **hold**, **present**, and **verify** credentials in a secure and interoperable way using **DIDComm messaging** and **OpenID for Verifiable Credential protocols**. This functionality forms the foundation of any Self-Sovereign Identity (SSI) ecosystem.

## Get started

Get started issuing and presenting credentials with your Credo agent, either with AnonCreds, JSON-LD or SD-JWT VC:

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup Credo Agent</strong></mark></td><td>Before you get started, you need to setup your Credo Agent. </td><td><a href="../set-up-agent.md">set-up-agent.md</a></td></tr><tr><td><mark style="color:blue;"><strong>AnonCreds</strong></mark></td><td>Issue and present AnonCreds signed by cheqd DIDs using Credo.</td><td><a href="anoncreds/">anoncreds</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td>Issue and present JSON-LD Verifiable Credentials signed by cheqd DIDs using Credo.</td><td><a href="json-ld/">json-ld</a></td></tr><tr><td><mark style="color:blue;"><strong>SD-JWT VC</strong></mark></td><td>Issue and present SD-JWT Verifiable Credentials signed by cheqd DIDs using Credo.</td><td><a href="sd-jwt/">sd-jwt</a></td></tr></tbody></table>

## Credential Format Support

Credo supports multiple credential formats and exchange protocols out of the box, including:

#### ✅ SD-JWT VC

* Alignment with the European Digital Identity Wallet initiative and standards.
* Selective disclosure baked in at credential-format level.
* Issuance and presentation support with **OpenID4VCI** and **OpenID4VP**.&#x20;

#### ✅ AnonCreds

* Ideal for privacy-preserving use cases that require **zero-knowledge proofs**, **selective disclosure**, and **non-revocation proofs**
* Backed by a **credential definition and revocation registry** stored on a supported ledger (e.g., **cheqd**, **Indy**)
* Common in **enterprise and government** deployments

#### ✅ W3C Verifiable Credentials (JSON-LD)

* Standards-compliant with the **W3C VC Data Model**
* Extensible for cheqd DID-Linked Resources and Trust Registries
* Issuance and presentation over DIDComm v2.
* Suitable for **web-native and mobile-first** use cases

## Alternatives

Below are a list of alternatives for using Credentials with cheqd support. Each offers a different set of protocols and underlying technical capabilities.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="/broken/pages/M8LzJPPc3H2SOjGsNFAQ">Broken link</a></td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py plugin supports full cheqd support for DIDs as well as Verifiable Credentials. </td><td><a href="../">..</a></td><td><a href="../../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The Veramo SDK plugin supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../veramo/credentials/issue-credential.md">issue-credential.md</a></td><td><a href="../../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../walt-id.md">walt-id.md</a></td><td><a href="../../../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td></tr></tbody></table>
