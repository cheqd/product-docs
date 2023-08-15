---
description: Service for resolving DIDs and dereferencing DID-Linked Resources
---

# ⬅ DID Resolver

## Overview

DID methods are expected to provide [standards-compliant methods of DID and DID Document ("DIDDoc") production](https://w3c.github.io/did-core/#production-and-consumption).

The [cheqd DID Resolver](https://github.com/cheqd/did-resolver) is designed to implement the [W3C DID _Resolution_ specification](https://w3c-ccg.github.io/did-resolution/) for [`did:cheqd`](../../architecture/adr-list/adr-001-cheqd-did-method.md) method. It also supports full DID URL dereferencing, [defined in our ADR here](../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md).

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup DID Resolver</strong></mark></td><td>Setup an instance of our DID Resolver to run it in your own environment.</td><td><a href="setup-did-resolver.md">setup-did-resolver.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Resolver ADR</strong></mark></td><td>Learn about the architecture for the cheqd DID Resolver and its different modes.</td><td><a href="../../architecture/adr-list/adr-004-did-registrar.md">adr-004-did-registrar.md</a></td></tr><tr><td><mark style="color:blue;"><strong>DID URL Dereferencing ADR</strong></mark></td><td>Understand how cheqd supports complex DID URL dereferencing for DIDs and DID-Linked Resources.</td><td><a href="../../architecture/adr-list/adr-005-did-resolution-and-did-url-dereferencing.md">adr-005-did-resolution-and-did-url-dereferencing.md</a></td></tr></tbody></table>
