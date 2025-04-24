---
description: Create digital Resources linked to cheqd DIDs on-ledger
---

# ↕️ Create DID-Linked Resources

**"DID-Linked Resources"** are identified with a [`did:cheqd` Decentralized Identifier](../../architecture/adr-list/adr-002-did-linked-resources.md) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**". Through the "**resource ID**" or a set of DID URL query parameters, applications are able to persistently access a digital resource on the cheqd network.

![Image showing a formatted DID URL for a cheqd resource](<../../.gitbook/assets/Formatting resource diagram.png>)

## Get started

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding DID-Linked Resources</strong></mark></td><td>Detailed guide explaining the context and technical composition of DID-Linked Resources on cheqd.</td><td><a href="understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Create a custom DID-Linked Resource for a schema, Trust Registry or other persistent data file.</td><td><a href="create-dlr.md">create-dlr.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Search DID-Linked Resources</strong></mark></td><td>Search and query for existing DID-Linked Resources on cheqd.</td><td><a href="search.md">search.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolution and DID URL Dereferencing</strong></mark></td><td>Architecture decision record explaining the different parameters for fetching DID Document data on cheqd, including DID-Linked Resources.</td><td><a href="../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md">adr-005-did-resolution-and-did-url-dereferencing.md</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for creating cheqd DID-Linked Resources.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../.gitbook/assets/Credo.png">Credo.png</a></td><td><a href="../../sdk/credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>ACA-Py</strong></mark></td><td>ACA-Py is a quickly expanding SDK, moving from the AnonCreds ecosystem towards wider support for EUDI.</td><td><a href="../../.gitbook/assets/ACA-Py.png">ACA-Py.png</a></td><td><a href="../../sdk/aca-py/">aca-py</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../.gitbook/assets/DIF.png">DIF.png</a></td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos-native CLI for creating transactions on cheqd. Should be used for <strong>testing purposes only</strong>.</td><td><a href="../../.gitbook/assets/cosmos and cheqd.png">cosmos and cheqd.png</a></td><td></td></tr></tbody></table>

## Partner SaaS support

cheqd DID-Linked Resources are supported in SaaS offerings from cheqd partners, making it possible to use did:cheqd DLRs across different platforms:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Godiddy</strong></mark></td><td>Godiddy from Danube Tech is a DID-specific SaaS offering focussed on enterprise registration and resolution of DIDs across multiple methods.</td><td><a href="../../.gitbook/assets/godiddy.png">godiddy.png</a></td><td><a href="https://godiddy.com/">https://godiddy.com/</a></td></tr><tr><td><mark style="color:blue;">Tr<strong>uvera (Dock)</strong></mark></td><td>Truvera is a full platform that supports credential issuance and management using cheqd as the primary DID Method.</td><td><a href="../../.gitbook/assets/Truvera.avif">Truvera.avif</a></td><td><a href="https://docs.truvera.io/">https://docs.truvera.io/</a></td></tr></tbody></table>

## DID-Linked Resource Write Pricing

Read up on our identity write pricing for cheqd Mainnet below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Identity Write Pricing</strong></mark></td><td>Click here to understand the price of DIDs and DID-Linked Resources on cheqd, compared to other identity networks.</td><td><a href="../../network/cheqd/identity-write-pricing.md">identity-write-pricing.md</a></td><td><a href="../../.gitbook/assets/cheqd-payments-icon.jpg">cheqd-payments-icon.jpg</a></td></tr></tbody></table>
