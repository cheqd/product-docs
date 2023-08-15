---
description: Create DIDs and identity keys using cheqd's DID Method (did:cheqd)
---

# 🆔 Create DIDs and Identity keys

A Decentralized Identifier "DID" is a globally unique identifier that does not require a centralized registration authority because it is registered with distributed ledger technology or other form of decentralized network.

{% hint style="info" %}
**Learn about DIDs** If you want to learn about what [DIDs are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did)
{% endhint %}

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Issuer DID</strong></mark></td><td>Create an Issuer DID using the did:cheqd DID method over REST API. </td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Subject DID</strong></mark></td><td>Create <strong>an off-ledger</strong> Subject DID using the <code>did:key</code> or <code>did:vda</code> DID methods.</td><td><a href="create-subject-did.md">create-subject-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Resolve DID</strong></mark></td><td>Resolve a DID to retrieve the associated DID Document. </td><td><a href="resolve-did.md">resolve-did.md</a></td></tr></tbody></table>

## cheqd DID Method

On-ledger DIDs created using the Credential Service use the cheqd DID Method. This is fully defined below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd DID Method</strong></mark></td><td>Click here to learn more about the cheqd DID method (did:cheqd).</td><td><a href="../../architecture/adr-list/adr-001-cheqd-did-method.md">adr-001-cheqd-did-method.md</a></td><td><a href="../../.gitbook/assets/cheqd logo docs.png">cheqd logo docs.png</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for creating cheqd DIDs.

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../sdk/aries-framework-js.md">aries-framework-js.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../sdk/ssi-kit.md">ssi-kit.md</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos CLI which directly communicates with the cheqd network. This should only be used for testing environments.</td><td><a href="../../advanced/tooling/cheqd-node-cli/">cheqd-node-cli</a></td></tr></tbody></table>
