---
description: >-
  Create DID-Linked Resources (DLRs) linked to Decentralized Identifiers (DIDs)
  on cheqd using ACA-Py.
---

# DID-Linked Resources

**"DID-Linked Resources"** are identified with a [`did:cheqd` Decentralized Identifier](https://docs.cheqd.io/product/architecture/adr-list/adr-002-did-linked-resources) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**". Through the "**resource ID**" or a set of DID URL query parameters, applications are able to persistently access a digital resource on the cheqd network.

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding DLRs</strong></mark></td><td>More details about DID-Linked Resources on Cheqd.</td><td></td><td><a href="../../../studio/did-linked-resources/understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding AnonCreds</strong></mark></td><td>More details about AnonCreds Object Method.</td><td></td><td><a href="../../../advanced/anoncreds/">anoncreds</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Schema</strong></mark></td><td>Create an AnonCreds Schema.</td><td></td><td><a href="create-schema.md">create-schema.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Credential Definition</strong></mark></td><td>Create an AnonCreds Credential Definition</td><td></td><td><a href="create-cred-def.md">create-cred-def.md</a></td></tr></tbody></table>

## Alternatives

Below are a list of SDK alternatives for creating cheqd DID-Linked Resources.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="broken-reference">Broken link</a></td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../credo/">credo</a></td><td><a href="../../../.gitbook/assets/Credo.png">Credo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../../advanced/did-registrar/">did-registrar</a></td><td><a href="../../../.gitbook/assets/DIF.png">DIF.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The Veramo SDK plugin supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../veramo/">veramo</a></td><td><a href="../../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos-native CLI for creating transactions on cheqd. Should be used for <strong>testing purposes only</strong>.</td><td></td><td><a href="../../../.gitbook/assets/cosmos and cheqd.png">cosmos and cheqd.png</a></td></tr></tbody></table>

t
