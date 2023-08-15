---
description: Veramo SDK plugin for cheqd identity functionality
---

# 🍈 Veramo SDK Plugin

## Overview

[Veramo SDK](https://veramo.io/) is a modular and pluggable client app SDK for decentralised identity and SSI applications. Our Veramo plugin - the [`@cheqd/did-provider-cheqd` NPM package](https://www.npmjs.com/package/@cheqd/did-provider-cheqd) - enables developers to interact with the cheqd ledger using Veramo SDK.

This package includes [Veramo SDK Agent methods](https://veramo.io/docs/veramo\_agent/plugins) for use with the [Veramo CLI NPM package](https://www.npmjs.com/package/@veramo/cli). It can also be consumed as an NPM package outside Veramo CLI for building your own applications with NPM.

The package's core functionality is borrowed from [Veramo Core NPM package](https://www.npmjs.com/package/@veramo/core) and extends this to include cheqd ledger functionality, such as creating and managing DIDs.

## Get started

Utilise cheqd's identity functionality through the Veramo SDK Plugin by installing the Veramo CLI and getting started below:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup Veramo CLI</strong></mark></td><td>Set up the Veramo CLI and required packages to use the Veramo SDK Plugin for cheqd.</td><td><a href="setup/">setup</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Credentials and Presentations</strong></mark></td><td>Issue fully W3C conformant Verifiable Credentials and Presentations</td><td><a href="credentials-and-presentations/">credentials-and-presentations</a></td></tr><tr><td><mark style="color:blue;"><strong>Charge for Credentials</strong></mark></td><td>Gate encrypted Status Lists with CHEQ using Payment Conditions.</td><td><a href="payments/">payments</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Decentralised Identifiers (DIDs)</strong></mark></td><td>Create fully W3C conformant Decentralised Identifiers (DIDs) on cheqd.</td><td><a href="did-operations/">did-operations</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Status Lists</strong></mark></td><td>Create Verifiable Credential Status List v2021 on-ledger, using DID-Linked Resources</td><td><a href="status-list-v2021/">status-list-v2021</a></td></tr><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resources</strong></mark></td><td>Create a variety of DID-Linked Resources, including schemas, status lists and trust registries on cheqd.</td><td><a href="did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>

## Why did we choose Veramo?

We chose to build a cheqd plugin into Veramo SDK for a few key reasons.

1. **Design Principles —** The Veramo SDK was designed to be highly flexible and modular making it highly scalable and fit for a lot of complex workflows. As a result, we felt it offered a route to minimise how much needs to be built from scratch. Through its flexible plug-in system, it’s easy to pick and choose which plug-ins are most beneficial, plus it’s possible to add in our custom packages where required which we knew would be necessary from Cosmos-based transactions.
2. **Developer Experience** — The Veramo SDK has been designed in a way that offers a fast end-to-end process. Ultimately, at cheqd, we want to reduce the amount of time our team spends on SDKs and so we can maintain our focus on building ledger functionality (i.e. building our implementation of the revocation registry and the credentials payment rails).
3. **Attractive & Simple CLI —** The Veramo core API is exposed by its CLI which makes it easy to create DIDs and VCs directly from a terminal or local cloud agent.
4. **Platform Agnostic** — The Veramo packages run on Node, Browsers and React & React Native right out of the box.

Veramo provides an excellent foundation for clients that want to build verifiable data applications. This is because Veramo Core, the Veramo CLI and any specific plugins are available as NPM packages, enabling:

1. Identity functionality to be carried out through a **native CLI interface**; or
2. Identity functionality to be **integrated directly into client applications through NPM packages**.

Using the DID-Provider-cheqd package, we have also been able to **reuse identity functionality** and abstract the complexity of cheqd/SDK and cheqd Network in less complex packages.

### `did:cheqd`-specific functionality

`did-provider-cheqd` is the first Veramo SDK plug-in that utilises the _DID Manager Update_ method to offer a full-body DIDDoc update for a DID on cheqd ledger, rather than individual field update transactions used more commonly in other DID methods such as [`did:ethr`](https://developer.uport.me/ethr-did/docs/index).

New DID creation can also be done by passing a full-body DIDoc payload in JSON, rather than having to assemble the document field-by-field.

> **Learn how to** [**configure and setup Veramo CLI for working with cheqd**](setup/) **network**
>
> Our [Veramo CLI setup guide](setup/) provides step-by-step instructions on how to configure your installation for cheqd network

## Architecture

![Veramo SDK for cheqd architecture](<../../.gitbook/assets/Veramo SDK packages.png>)

_Figure 1:_ Veramo SDK for cheqd architecture with components ([editable Draw.io version](../../.gitbook/assets/veramo-sdk-for-cheqd.drawio))

`did-provider-cheqd` consumes functionality that exists within the [`@cheqd/sdk` NPM package](https://www.npmjs.com/package/@cheqd/sdk) in a way that complies to the Veramo `AbstractIdentifierProvider`. (You don't need to call this package separately as it's already included as a dependency in `package.json`.)

This package works alongside other base Veramo packages:

* [`@veramo/core`](https://www.npmjs.com/package/@veramo/core)
* [`@veramo/cli`](https://www.npmjs.com/package/@veramo/cli)
* [`@veramo/credential-w3c`](https://www.npmjs.com/package/@veramo/credential-w3c)

The `@cheqd/sdk` package adds Cosmos SDK specific functionality such as signing transactions, paying for ledger fees, etc.

For identity-related keys, this plugin uses [Veramo's Key Management System (KMS)](https://www.npmjs.com/package/@veramo/key-manager) to manage keys for create and update operations.

Find out about [other Veramo plug-ins in their official documentation](https://veramo.io/docs/veramo\_agent/plugins/).

## Supported Credential types

Our [Veramo SDK Plugin](./) natively supports the following Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>JSON (JWT)</strong></mark></td><td><strong>JSON Web Token (JWT)</strong> Credentials are a simple way to transmit Trusted Data as a JSON object.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld</a></td></tr></tbody></table>

## Working with Veramo SDK for cheqd outside of Veramo CLI

We provide a quick-start [setup guide for working with cheqd network using Veramo CLI](setup/).

You can use our Veramo plugin in non-CLI contexts as well, for example, for integrating into custom applications

Depending on the type of application you are looking to develop, you will need to install a different set of packages.

For other applications, see:

* [Veramo Node.js tutorial](https://veramo.io/docs/node\_tutorials/node\_setup\_identifiers)
* [Veramo React tutorial](https://veramo.io/docs/react\_tutorials/react\_setup\_resolver)
* [Veramo React Native tutorial](https://veramo.io/docs/react\_native\_tutorials/react\_native\_1\_setup\_identifiers)
