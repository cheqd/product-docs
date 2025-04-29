---
description: cheqd AnonCreds Object Method Specification
---

# ⚡ AnonCreds Object Method

## cheqd AnonCreds Object Method

cheqd intends to directly support AnonCreds using its [DID-Linked Resource module](../../architecture/adr-list/adr-002-did-linked-resources.md) in an AnonCreds Object Method. With its resource module, cheqd will identify each on-ledger resource with a [DID Core](https://www.w3.org/TR/did-core/) compliant [DID URL](https://www.w3.org/TR/did-core/). This DID URL will be able to be dereferenced in order to fetch the resource and associated metadata.

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Schemas</strong></mark></td><td>Create Schemas using DID-Linked Resources to support AnonCreds on cheqd.</td><td><a href="schema.md">schema.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Credential Definitions</strong></mark></td><td>Create CredDefs using DID-Linked Resources to support AnonCreds on cheqd.</td><td><a href="credential-definition.md">credential-definition.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Revocation Registry Definitions</strong></mark></td><td>Create Revocation Registry Definitions using DID-Linked Resources to support AnonCreds on cheqd.</td><td><a href="revocation-registry-definition.md">revocation-registry-definition.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Revocation Status Lists</strong></mark></td><td>Create Revocation Status Lists using DID-Linked Resources to support AnonCreds on cheqd.</td><td><a href="revocation-status-list.md">revocation-status-list.md</a></td></tr></tbody></table>

## Get started with AnonCreds

cheqd fully supports AnonCreds in multiple SDKs, including issuance, verification and revocation. See our documentation below to get started.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../.gitbook/assets/Credo.png">Credo.png</a></td><td><a href="../../sdk/credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py (Aries Cloud Agent Python) is an SDK that can issue AnonCreds, JSON-LD and SD-JWT credentials with full cheqd support.</td><td><a href="../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td><td><a href="../../sdk/aca-py/credentials/">credentials</a></td></tr></tbody></table>

## Partner SaaS products

You can use SaaS products from our partners to issue AnonCreds as well, with cheqd DID support under the hood!

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Paradym</strong></mark></td><td>Paradym by Animo Solutions fully supports <strong>EU Architecture &#x26; Reference Framework</strong> standards with cheqd under the hood.</td><td><a href="../../.gitbook/assets/paradym-for-website.png">paradym-for-website.png</a></td><td><a href="https://paradym.id/">https://paradym.id/</a></td></tr><tr><td><mark style="color:blue;"><strong>Hovi</strong></mark></td><td>Hovi provides an all-in-one platform for issuing and managing Verifiable Credentials with cheqd DIDs supported.</td><td><a href="../../.gitbook/assets/Hovi.png">Hovi.png</a></td><td><a href="https://www.hovi.id/">https://www.hovi.id/</a></td></tr></tbody></table>

## Why is AnonCreds support important?

While AnonCreds are only one _flavour_ of Verifiable Credentials, they are currently in a functional state and are heavily used by cheqd's partners. Other Credential types, such as JSON-LD with BBS+ signatures, can provide a lot of equivalent functionality, but are currently not production ready.

Therefore, it is important for cheqd to provide support for AnonCreds in order to enable partners with existing clients using AnonCreds to use cheqd and existing Indy ledgers concurrently, within existing applications.

## What does AnonCreds support look like?

Our aim is to support the functionality enabled by [identity-domain transactions in by Hyperledger Indy](https://github.com/hyperledger/indy-node/blob/master/docs/source/transactions.md) into `cheqd-node`. This will reach the goal of allowing use cases of existing SSI networks on Hyperledger Indy to be supported by the cheqd network.

Importantly, we want to make sure that this work is done in a manner which brings AnonCreds closer to W3C compliance and wide-scale interoperability.
