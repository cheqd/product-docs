---
description: Get started with cheqd's identity functionality and product offerings.
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Product Overview

[**cheqd**](https://cheqd.io/) is a purpose-built network for decentralised identity. This documentation site provides **technical and product information** for all **identity features & functionality** on the cheqd network.

## Integrate with cheqd

cheqd maintains an array of products and packages with **varying levels of integration complexity** to allow its partners and customers to have multiple ways of plugging into cheqd's identity functionality. Via these different packages, customers and partners can create cheqd DIDs, DID-Linked Resources, Status Lists, Trust Registries and Schemas, as well as use cheqd's Credential Payments model.

There are three core ways of integrating and building with cheqd:

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><strong>cheqd Studio</strong></td><td><em>Low complexity</em></td><td>Customers can easily integrate cheqd's identity functionality into their existing applications through hitting simple REST APIs using cheqd Studio. </td><td><a href=".gitbook/assets/credential-service-image.png">credential-service-image.png</a></td><td><a href="getting-started/studio/">studio</a></td></tr><tr><td><strong>Software Development Kits (SDKs)</strong></td><td><em>Moderate complexity</em></td><td>To have greater flexibility, customers can build identity solutions through a set of SDKs to meet different technical stacks and requirements.</td><td><a href=".gitbook/assets/SDKs-image.png">SDKs-image.png</a></td><td><a href="sdk/understanding-sdks.md">understanding-sdks.md</a></td></tr><tr><td><strong>Direct integration with ledger code (cheqd SDK)</strong></td><td><em>High complexity</em></td><td>For full customisability and flexibility, the cheqd SDK plugs directly into cheqd Network and is responsible for communicating with the core ledger code. </td><td><a href=".gitbook/assets/cheqd-sdk-image.png">cheqd-sdk-image.png</a></td><td><a href="advanced/tooling/advanced.md">advanced.md</a></td></tr></tbody></table>

## What functionality does cheqd offer?

cheqd's identity functionality is fully standards compliant, ensuring interoperability and no vendor lock-in. cheqd's tooling and different product offerings offer a variety of building blocks for any Trusted Data Market.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Decentralised Identifiers (DIDs)</strong></mark></td><td>Fully <a href="https://www.w3.org/TR/did-core/">W3C DID Core</a> conformant Digital identifiers for establishing a root of trust in a given ecosystem. Utilising <a href="architecture/adr-list/adr-001-cheqd-did-method.md">cheqd's DID method (did:cheqd)</a>.</td><td><a href="studio/dids/">dids</a></td></tr><tr><td><mark style="color:blue;"><strong>Build Trust Registries</strong></mark> </td><td>Create industry-leading trust registries, using chains of Verifiable Accreditations, to secure your identity ecosytem.</td><td><a href="studio/trust-registries/">trust-registries</a></td></tr><tr><td><mark style="color:blue;"><strong>Charge for Credentials</strong></mark></td><td>Setup encrypted Status Lists and other Resources with <a href="studio/payments/learn/access-control-conditions.md">Payment Conditions to unlock</a>, allowing issuers to set a price for verifying a Credential Status. </td><td><a href="studio/payments/">payments</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Credentials and Presentations</strong></mark></td><td>Fully <a href="https://www.w3.org/TR/vc-data-model/">W3C VC Data Model</a> conformant Verifiable Credentials for establishing trust. Supporting JSON, JSON-LD, SD-JWT and AnonCreds through cheqd APIs and via cheqd's Partners.</td><td><a href="studio/credentials/">credentials</a></td></tr><tr><td><mark style="color:blue;"><strong>Create Status Lists on-ledger</strong></mark></td><td>Sophisticated and first-of-its-kind cryptographically verifiable on-ledger files for establishing Trust and Reputability in a Verifiable Credential, utilising DID-Linked Resources. </td><td><a href="studio/status-lists/">status-lists</a></td></tr><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resources</strong></mark></td><td>Digital resources, leveraging the cryptographic verifiability of DIDs and patterns of DID Core to anchor schemas, trust registries, status lists and more. </td><td><a href="studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>

## Industry Standard DID Support

If you want to utilise cheqd DIDs or DID-Linked Resources within applications without a specific cheqd integration, you can incorporate a Universal Registrar or Universal Resolver driver to support [did:cheqd](architecture/adr-list/adr-001-cheqd-did-method.md) alongside other commonly adopted DID methods.&#x20;

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Registrar (and Universal Registrar Driver)</strong></mark></td><td><em>Low complexity</em></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td></td><td><a href="advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolver (and Universal Resolver Driver)</strong></mark></td><td><em>Low complexity</em></td><td>Service for resolving cheqd DIDs and DID URLs. Feature complete with query-based DID URL dereferencing. Full integration and driver within the <a href="https://dev.uniresolver.io/">Universal Resolver</a>.</td><td></td><td><a href="advanced/did-resolver/">did-resolver</a></td></tr></tbody></table>

## Get started with cheqd Studio

cheqd Studio is the easiest route to get started with cheqd's identity functionality, or for testing with very low integration effort. Using simple REST APIs, it is possible to integrate cheqd Studio in a few lines of code.

Under the hood, cheqd Studio utilises the [Veramo SDK cheqd Plugin](sdk/veramo/), providing the most feature complete set of functionality and tooling.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Get started with cheqd Studio</strong></mark></td><td>Click here to start your journey with cheqd Studio, create your account and begin using cheqd's underlying functionality.</td><td><a href="getting-started/studio/">studio</a></td><td><a href=".gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr></tbody></table>

If you'd rather build a deeper integration [using a Software Development Kit (SDK)](sdk/understanding-sdks.md) or lower level package, we've created a simple diagram to show how our packages are structured below.

## Understanding cheqd's capabilities

cheqd supports a wide array of standards and protocols via different Software Development Kits (SDKs) and integrations.

<figure><picture><source srcset=".gitbook/assets/cheqd Capabilities (Dark mode).png" media="(prefers-color-scheme: dark)"><img src=".gitbook/assets/cheqd capabilities (light mode).png" alt=""></picture><figcaption><p>cheqd Capability diagram</p></figcaption></figure>

## Understanding the _structure_ of cheqd's packages

One of cheqd's primary motives is to make itself accessible to the widest set of identity applications possible. To accomplish this, cheqd has built a **flexible set of packages and tooling** to accommodate its identity capabilities into a **broad set of external SDKs and applications**.&#x20;

These can be represented through the visual below:

<figure><img src=".gitbook/assets/cheqd packages.png" alt=""><figcaption><p>cheqd packages diagram</p></figcaption></figure>

## Partner SaaS products

You can also leverage SaaS products from our partners to build high-quality credential ecosystems on top of cheqd’s infrastructure:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Paradym</strong></mark></td><td>Paradym by Animo Solutions fully supports <strong>EU Architecture &#x26; Reference Framework</strong> standards with cheqd under the hood.</td><td><a href=".gitbook/assets/paradym-for-website.png">paradym-for-website.png</a></td><td><a href="https://paradym.id/">https://paradym.id/</a></td></tr><tr><td><mark style="color:blue;">Tr<strong>uvera (Dock)</strong></mark></td><td>Truvera is a full platform that supports credential issuance and management using cheqd as the primary DID Method.</td><td><a href=".gitbook/assets/Truvera.avif">Truvera.avif</a></td><td><a href="https://docs.truvera.io/">https://docs.truvera.io/</a></td></tr><tr><td><mark style="color:blue;"><strong>Hovi</strong></mark></td><td>Hovi provides an all-in-one platform for issuing and managing Verifiable Credentials with cheqd DIDs supported.</td><td><a href=".gitbook/assets/Hovi.png">Hovi.png</a></td><td><a href="https://www.hovi.id/">https://www.hovi.id/</a></td></tr><tr><td><mark style="color:blue;"><strong>Cloud Platform (Walt.id)</strong></mark></td><td>Cloud Platform by Walt.id similarly fully supports <strong>EU Architecture &#x26; Reference Framework</strong> standards with cheqd under the hood.</td><td><a href=".gitbook/assets/walt.id-cloud-platform.png">walt.id-cloud-platform.png</a></td><td><a href="https://walt.id/waitlist">https://walt.id/waitlist</a></td></tr><tr><td><mark style="color:blue;"><strong>Godiddy</strong></mark></td><td>Godiddy from Danube Tech is a DID-specific SaaS offering focussed on enterprise registration and resolution of DIDs across multiple methods.</td><td><a href=".gitbook/assets/godiddy.png">godiddy.png</a></td><td><a href="https://godiddy.com/">https://godiddy.com/</a></td></tr></tbody></table>

## Get involved

The [cheqd Discord](https://discord.com/invite/4VZszdE7HH) is our primary chat channel for the open-source community, software developers, and node operators.

Please reach out to us there for discussions, help, and feedback on the project.

## 🙋 Find us elsewhere

[![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge\&logo=telegram\&logoColor=white)](https://t.me/cheqd) [![Discord](https://img.shields.io/badge/Discord-7289DA?style=for-the-badge\&logo=discord\&logoColor=white)](http://cheqd.link/discord-github) [![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge\&logo=twitter\&logoColor=white)](https://twitter.com/intent/follow?screen_name=cheqd_io) [![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge\&logo=linkedin\&logoColor=white)](http://cheqd.link/linkedin)  [![Medium](https://img.shields.io/badge/Medium-12100E?style=for-the-badge\&logo=medium\&logoColor=white)](https://blog.cheqd.io) [![YouTube](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge\&logo=youtube\&logoColor=white)](https://www.youtube.com/channel/UCBUGvvH6t3BAYo5u41hJPzw/)
