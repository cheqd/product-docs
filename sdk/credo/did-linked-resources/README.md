---
description: Create cheqd DID-Linked Resources using Credo
---

# DID-Linked Resources

**DID-Linked Resources** are digital files or data blobs that are permanently and verifiably linked to a specific `did:cheqd` identifier. Each resource is stored on the cheqd ledger and assigned a **UUID**, called the **Resource ID**, which acts as a unique, persistent reference.

Applications can retrieve these resources using either:

* A **fully-qualified Resource ID** (DID + `/resources/<UUID>`)
* Or a **DID URL** with query parameters like `resourceName` and `resourceType`

### Common Use Cases

DID-Linked Resources enable storage of a wide variety of DID-associated metadata, including:

* Trust Registry entries
* Status Lists (e.g. credential revocation)
* Logos and brand assets
* Governance and policy documents
* Credential Schemas and Definitions



## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Create a generic DID-Linked Resource tied to a cheqd DID.</td><td></td><td><a href="create-did-linked-resource.md">create-did-linked-resource.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding DLRs</strong></mark></td><td>More details about DID-Linked Resources on Cheqd.</td><td></td><td><a href="../../../studio/did-linked-resources/understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding AnonCreds</strong></mark></td><td>More details about AnonCreds Object Method.</td><td></td><td><a href="../../../advanced/anoncreds/">anoncreds</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Schema</strong></mark></td><td>Create an AnonCreds Schema.</td><td></td><td><a href="create-anoncreds-schema.md">create-anoncreds-schema.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Credential Definition</strong></mark></td><td>Create an AnonCreds Credential Definition</td><td></td><td><a href="create-anoncreds-credential-definition.md">create-anoncreds-credential-definition.md</a></td></tr></tbody></table>

## Alternatives

Below are a list of SDK alternatives for creating cheqd DID-Linked Resources.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="broken-reference">Broken link</a></td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../../advanced/did-registrar/">did-registrar</a></td><td><a href="../../../.gitbook/assets/DIF.png">DIF.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The Veramo SDK plugin supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../veramo-plugin/">veramo-plugin</a></td><td><a href="../../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos-native CLI for creating transactions on cheqd. Should be used for <strong>testing purposes only</strong>.</td><td></td><td><a href="../../../.gitbook/assets/cosmos and cheqd.png">cosmos and cheqd.png</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py (Aries Cloud Agent Python) is an SDK that can issue AnonCreds, JSON-LD and SD-JWT credentials with full cheqd support.</td><td><a href="../../aca-py/">aca-py</a></td><td><a href="../../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td></tr></tbody></table>
