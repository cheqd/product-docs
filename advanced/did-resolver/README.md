---
description: >-
  Service for resolving Decentralized Identifiers (DIDs) and dereferencing
  DID-Linked Resources on cheqd.
---

# ⬅️ DID Resolver

## Overview

DID methods are expected to provide [standards-compliant methods of DID and DID Document ("DIDDoc") production](https://w3c.github.io/did-core/#production-and-consumption).

The [cheqd DID Resolver](https://www.w3.org/TR/did-resolution/) is designed to implement the [W3C DID _Resolution_ specification](https://www.w3.org/TR/did-resolution/) for [`did:cheqd`](../../architecture/adr-list/adr-001-cheqd-did-method.md) method. It also supports full DID URL dereferencing, [defined in our ADR here](../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md).

Our DID resolver is a package which can be implemented directly into clients' own infrastructure as a library written in Golang. This provides full support for cheqd's resolver, and can be run by anyone, creating a secure and client-controlled environment for resolving cheqd DIDs.

Our DID resolver is also available as [a supported driver in Universal Resolver](https://github.com/decentralized-identity/universal-resolver), a project maintained by DIF which hosts drivers of many different DID Resolvers in a compatible and easy-to-integrate format (Docker Containers).

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup DID Resolver</strong></mark></td><td>Setup an instance of our DID Resolver to run it in your own environment.</td><td><a href="setup-did-resolver.md">setup-did-resolver.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolver ADR</strong></mark></td><td>Learn about the architecture for the cheqd DID Resolver and its different modes.</td><td><a href="../../architecture/adr-list/adr-004-did-registrar.md">adr-004-did-registrar.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID URL Dereferencing ADR</strong></mark></td><td>Understand how cheqd supports complex DID URL dereferencing for DIDs and DID-Linked Resources.</td><td><a href="../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md">adr-005-did-resolution-and-did-url-dereferencing.md</a></td></tr></tbody></table>

## What is the core business value of this work?

Having multiple implementations of a DID Resolver accommodates for different clients, developers and customers - each with different needs. The flexibility and modular architecture exhibited here will allow cheqd DIDs to be resolved simply and securely within closed, controlled ecosystems with tight security protocols - as well as by community members who want to try our our identity functionality. Catering to both parties' needs makes the cheqd DID Resolver valuable in both everyday use, and for enterprise use.

## Want to test it out?

You can see our resolver in action, resolving our first DID here:

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:mainnet:Ps1ysXP2Ae6GBfxNhNQNKN" %}
cheqd first DID in DID resolver
{% endembed %}
