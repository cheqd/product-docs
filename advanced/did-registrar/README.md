---
description: REST API service for creating cheqd DIDs and DID-Linked Resources
---

# ➡ DID Registrar

## Introduction

The [Universal Registrar](https://uniregistrar.io/) is an open source application created by the [Decentralized Identity Foundation (DIF)](https://identity.foundation/) which aims to make it far easier to create, update and deactivate [Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) across a range of DID Methods without full integration.

The aim of the Universal Registrar is similar to the Universal Resolver; to transform method-specific APIs for DID transactions into a common format for client applications to easily call.

Not only does it make it easier for client applications to support DIDs from multiple DID methods, but it also makes it far quicker and easier to create, update and deactivate DIDs — as it calls the [method-specific driver](https://github.com/decentralized-identity/universal-registrar#drivers) with a common API.

Therefore, the barrier for integrating cheqd DIDs into existing client applications has been greatly reduced by the Registrar. Instead of having to integrate with the cheqd SDK, applications can now create a simple workflow to call the relevant APIs for issuing, updating or deactivating cheqd DIDs and creating DID-Linked Resources.

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup DID Registrar</strong></mark></td><td>Setup DID Registrar yourself or use the Universal Registrar interface.</td><td><a href="did-registrar-setup.md">did-registrar-setup.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create a DID simply over REST API.</td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create a DID-Linked Resource</strong></mark></td><td>Create a DID-Linked Resource simply over REST API.</td><td><a href="create-resource.md">create-resource.md</a></td></tr></tbody></table>

## Watch our demo video

{% embed url="https://www.youtube.com/watch?v=cLrgO5IY8P8" %}
Creatbg a DID using DID Regisrar
{% endembed %}

## Alternatives

There are plenty of other places you can go to get started creating cheqd DIDs. Simply pick the option which is best suited for your needs:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credential Service</strong></mark></td><td><strong>Credential Service</strong> is a SaaS product offering the easiest and quickest way to issue Credentials, using REST APIs and very little integration effort.</td><td><a href="broken-reference">Broken link</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../sdk/aries-framework-js.md">aries-framework-js.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../sdk/ssi-kit.md">ssi-kit.md</a></td></tr><tr><td><mark style="color:blue;"><strong>cheqd Cosmos CLI</strong></mark></td><td>Cosmos CLI which directly communicates with the cheqd network. This should only be used for <strong>testing environments</strong>.</td><td><a href="../tooling/cheqd-node-cli/">cheqd-node-cli</a></td></tr></tbody></table>
