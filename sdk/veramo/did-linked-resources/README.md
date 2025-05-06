---
description: >-
  Create DID-Linked Resources (DLRs) linked to Decentralized Identifiers (DIDs)
  on cheqd using Veramo.
---

# DID-Linked Resources

**"DID-Linked Resources"** are identified with a [`did:cheqd` Decentralized Identifier](../../../architecture/adr-list/adr-002-did-linked-resources.md) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**". Through the "**resource ID**" or a set of DID URL query parameters, applications are able to persistently access a digital resource on the cheqd network.

![Image showing a formatted DID URL for a cheqd resource](<../../../.gitbook/assets/Formatting resource diagram.png>)

## Get started

Use the tutorials below to start creating DID-Linked Resoures using Veramo.

<table data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Create a custom DID-Linked Resource for a schema, Trust Registry or other persistent data file.</td><td><a href="../../../studio/did-linked-resources/create-dlr.md">create-dlr.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Status List</strong></mark></td><td>Create an on-ledger Status List associated with a DID.</td><td><a href="../../../studio/status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding DID-Linked Resources</strong></mark></td><td>Detailed guide explaining the context and technical composition of DID-Linked Resources on cheqd.</td><td><a href="../../../studio/did-linked-resources/understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolution and DID URL Dereferencing</strong></mark></td><td>Architecture decision record explaining the different parameters for fetching DID Document data on cheqd, including DID-Linked Resources</td><td><a href="../../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md">adr-005-did-resolution-and-did-url-dereferencing.md</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for creating cheqd DID-Linked Resources.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="broken-reference">Broken link</a></td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../../advanced/did-registrar/">did-registrar</a></td><td><a href="../../../.gitbook/assets/DIF.png">DIF.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../credo/">credo</a></td><td><a href="../../../.gitbook/assets/Credo.png">Credo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos-native CLI for creating transactions on cheqd. Should be used for <strong>testing purposes only</strong>.</td><td></td><td><a href="../../../.gitbook/assets/cosmos and cheqd.png">cosmos and cheqd.png</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py (Aries Cloud Agent Python) is an SDK that can issue AnonCreds, JSON-LD and SD-JWT credentials with full cheqd support.</td><td><a href="../../aca-py/">aca-py</a></td><td><a href="../../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td></tr></tbody></table>
