# 🔀 Resolver

## Overview

DID resolution is the process of **resolving a DID to fetch a DID document** by using the "Read" operation of the applicable DID method, in our case, the [cheqd DID method](https://github.com/cheqd/node-docs/blob/adr-did-resolver/architecture/adr-list/adr-002-cheqd-did-method.md).

cheqd has defined multiple implementations of a DID Resolver, for different audiences:

* A **full** cheqd DID Resolver, as a library written in Go, or as a standalone web service (the goal of which is to generate a spec compliant DIDDoc, based on the [cheqd DID method](https://github.com/cheqd/node-docs/blob/adr-did-resolver/architecture/adr-list/adr-002-cheqd-did-method.md)), through communicating with a cheqd node at the gPRC endpoint;
* A **light** cheqd DID Resolver, this is a JavaScript-based proxy resolver designed so it can work on Cloudflare Workers, but the same can also be spun up as the “**light**” profile in our Docker Compose (for Universal Resolver);
* A [**Universal Resolver Driver**](https://github.com/decentralized-identity/universal-resolver) (the goal of this, as a Docker Compose, is to provide greater accessibility to third parties resolving cheqd DIDs, within a more standardised and accessible format).
  * Full cheqd DID Resolver compiles into: **full profile Universal Resolver Driver**
  * Light cheqd DID Resolver compiles into: **light profile Universal Resolver Driver**

Each implementation of the cheqd DID Resolver is **decoupled from the cheqd network**, which means updating the resolver does not require updating the application on the node side. This avoids having to go through an on-ledger governance vote and voting period to make a change.&#x20;

{% content-ref url="full-did-resolver.md" %}
[full-did-resolver.md](full-did-resolver.md)
{% endcontent-ref %}

{% content-ref url="universal-resolver-driver.md" %}
[universal-resolver-driver.md](universal-resolver-driver.md)
{% endcontent-ref %}
