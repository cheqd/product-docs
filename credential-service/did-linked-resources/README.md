---
description: Create digital Resources linked to cheqd DIDs on-ledger
---

# ↕ Create DID-Linked Resources

**"DID-Linked Resources"** are identified with a [`did:cheqd` Decentralized Identifier](../../architecture/adr-list/adr-002-did-linked-resources.md) with a [Universally Unique Identifier (UUID)](https://www.uuidgenerator.net/) that acts as a permanently-accessible link to fetch the resources from the cheqd ledger. We refer to this as the "**resource ID**". Through the "**resource ID**" or a set of DID URL query parameters, applications are able to persistently access a digital resource on the cheqd network.

![Image showing a formatted DID URL for a cheqd resource](<../../.gitbook/assets/Formatting resource diagram.png>)

## Get started

<table data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Create a custom DID-Linked Resource for a schema, Trust Registry or other persistent data file.</td><td><a href="create-dlr.md">create-dlr.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Status List</strong></mark></td><td>Create an on-ledger Status List associated with a DID.</td><td><a href="../status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Understanding DID-Linked Resources</strong></mark></td><td>Detailed guide explaining the context and technical composition of DID-Linked Resources on cheqd.</td><td><a href="understanding-dlrs/">understanding-dlrs</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolution and DID URL Dereferencing</strong></mark></td><td>Architecture decision record explaining the different parameters for fetching DID Document data on cheqd, including DID-Linked Resources.</td><td><a href="../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md">adr-005-did-resolution-and-did-url-dereferencing.md</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for creating cheqd DID-Linked Resources.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../sdk/aries-framework-js.md">aries-framework-js.md</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos-native CLI for creating transactions on cheqd. Should be used for <strong>testing purposes only</strong>.</td><td></td></tr></tbody></table>
