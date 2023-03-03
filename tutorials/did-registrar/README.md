# ➡ DID Registrar

## Introduction

The [Universal Registrar](https://uniregistrar.io/) is an open source application created by the [Decentralized Identity Foundation (DIF)](https://identity.foundation/) which aims to make it far easier to create, update and deactivate [Decentralized Identifiers (DIDs)](https://www.w3.org/TR/did-core/) across a range of DID Methods without full integration.

The aim of the Universal Registrar is similar to the Universal Resolver; to transform method-specific APIs for DID transactions into a common format for client applications to easily call.

Not only does it make it easier for client applications to support DIDs from multiple DID methods, but it also makes it far quicker and easier to create, update and deactivate DIDs — as it calls the [method-specific driver](https://github.com/decentralized-identity/universal-registrar#drivers) with a common API.

Therefore, the barrier for integrating cheqd DIDs into existing client applications has been greatly reduced by the Registrar. Instead of having to integrate with the cheqd SDK, applications can now create a simple workflow to call the relevant APIs for issuing, updating or deactivating cheqd DIDs and creating DID-Linked Resources.

## Getting started

* [Setting up DID Registrar](did-registrar-setup.md)
* [Create a DID using DID Registrar](create-a-did-using-registrar.md)
* [Architecture Decision Record - DID Registrar](../../architecture/adr-list/adr-004-did-registrar.md)
* [DID Registrar Driver](https://did-registrar.cheqd.net/api-docs/)
