---
description: >-
  Issue and present Verifiable Credentials, signed by cheqd Decentralized
  Identifiers (DIDs), using ACA-Py.
---

# Verifiable Credentials and Presentations

{% hint style="info" %}
:books: **Learn about Verifiable Credentials and Presentations**

If you want to learn more about what [Verifiable Credentials and Presentations are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc)
{% endhint %}

**Aries Cloud Agent Python (ACA-Py)** provides full support for working with **Verifiable Credentials (VCs)** and **Verifiable Presentations (VPs)**, based on the standards defined by the **W3C** and the **Aries RFCs**.

ACA-Py enables agents to **issue**, **hold**, **present**, and **verify** credentials in a secure and interoperable way using **DIDComm messaging protocols**. This functionality forms the foundation of any Self-Sovereign Identity (SSI) ecosystem.

## Get started

Get started issuing and presenting credentials with your ACA-Py agent, either with AnonCreds or JSON-LD:

### AnonCreds

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue AnonCreds Credentials</strong></mark></td><td>Issue AnonCreds Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="anoncreds/issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Present AnonCreds Credentials</strong></mark></td><td>Present AnonCreds Credentials using ACA-Py.</td><td><a href="anoncreds/present-credential.md">present-credential.md</a></td></tr></tbody></table>

### JSON-LD

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue JSON-LD Credentials</strong></mark></td><td>Issue JSON-LD Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="json-ld/issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Present JSON-LD Credentials</strong></mark></td><td>Present JSON-LD Credentials signed by cheqd DIDs using ACA-Py.</td><td><a href="json-ld/present-credential.md">present-credential.md</a></td></tr></tbody></table>

## Credential Format Support

ACA-Py supports multiple credential formats out of the box, including:

#### ✅ AnonCreds

* Ideal for privacy-preserving use cases that require **zero-knowledge proofs**, **selective disclosure**, and **non-revocation proofs**
* Backed by a **credential definition and revocation registry** stored on a supported ledger (e.g., **cheqd**, **Indy**)
* Common in **enterprise and government** deployments

#### ✅ W3C Verifiable Credentials (JSON-LD)

* Standards-compliant with the **W3C VC Data Model**
* Extensible for cheqd DID-Linked Resources and Trust Registries
* Suitable for **web-native and mobile-first** use cases

> 🧩 ACA-Py allows issuing, presenting, and verifying **both AnonCreds and JSON-LD credentials** using the same agent architecture.

## Alternatives

Below are a list of alternatives for using Credentials with cheqd support. Each offers a different set of protocols and underlying technical capabilities.

<table data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td><td><a href="/broken/pages/M8LzJPPc3H2SOjGsNFAQ">Broken link</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../../.gitbook/assets/Credo.png">Credo.png</a></td><td><a href="../../credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The Veramo SDK plugin supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td><td><a href="../../veramo/credentials/issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td><td><a href="../../walt-id.md">walt-id.md</a></td></tr></tbody></table>
