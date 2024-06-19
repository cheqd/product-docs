---
description: Create DIDs and identity keys using cheqd's DID Method (did:cheqd)
---

# 🆔 Create DIDs and Identity keys

A Decentralized Identifier "DID" is a globally unique identifier that does not require a centralized registration authority because it is registered with distributed ledger technology or other form of decentralized network.

{% hint style="info" %}
**Learn about DIDs** If you want to [learn about what DIDs are, please go over to our learning site here](https://learn.cheqd.io/decentralised-identity/dids).
{% endhint %}

## What options do I have for creating DIDs on cheqd?

There are many different ways to create DIDs on cheqd, with options for easy integration (e.g. cheqd Studio) and more bespoke integrations (e.g. Credo and Veramo). Below are a list of options for creating cheqd DIDs.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../sdk/credo.md">credo.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <strong>European Architecture and Reference Framework (ARF)</strong> standards for identity, with full cheqd support. </td><td><a href="../../sdk/ssi-kit.md">ssi-kit.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos CLI which directly communicates with the cheqd network. This should only be used for testing environments.</td><td><a href="../../advanced/tooling/cheqd-node-cli/">cheqd-node-cli</a></td></tr></tbody></table>

## Get started with cheqd Studio

The easiest way to start creating DIDs is with our product cheqd Studio, allowing users to build and create `did:cheqd` DIDs using REST APIs.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Issuer DID</strong></mark></td><td>Create an Issuer DID using the did:cheqd DID method over REST API. </td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Subject DID</strong></mark></td><td>Create <strong>an off-ledger</strong> Subject DID using the <code>did:key</code> or <code>did:vda</code> DID methods.</td><td><a href="create-subject-did.md">create-subject-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Resolve DID</strong></mark></td><td>Resolve a DID to retrieve the associated DID Document. </td><td><a href="resolve-did.md">resolve-did.md</a></td></tr></tbody></table>

## Read up on our DID Method

DIDs created using cheqd Studio use the `did:cheqd` DID Method. This is fully defined below, as well as the associated ledger-write fees:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd DID Method</strong></mark></td><td>Click here to learn more about the cheqd DID method (did:cheqd).</td><td><a href="../../architecture/adr-list/adr-001-cheqd-did-method.md">adr-001-cheqd-did-method.md</a></td><td><a href="../../.gitbook/assets/cheqd logo docs.png">cheqd logo docs.png</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Write Pricing</strong></mark></td><td>Click here to understand the price of DIDs and other resources on cheqd, compared to other identity networks.</td><td></td><td><a href="../../.gitbook/assets/cheqd-payments-icon.jpg">cheqd-payments-icon.jpg</a></td></tr></tbody></table>
