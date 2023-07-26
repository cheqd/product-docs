# 🔗 DID-Linked Resources

## Understanding DID-Linked Resources

* [Understanding DID-Linked Resources](../../guides/did-linked-resources/)
* [ADR 002: DID-Linked Resources](../../architecture/adr-list/adr-002-did-linked-resources.md)

## Credential Service

The easiest way to issue DID-Linked Resources is with our "Credentials-as-a-Service" offering - a set of hosted APIs which can create DIDs, DID-Linked Resources and issue Credentials in a few clicks or minimal lines of code.

Currently, Credential Service supports both JSON (JWT) and JSON-LD Credentials.&#x20;

See the links below to get started:

* [Credential Service](../credential-service/)
  * [User Guide](../credential-service/user-guide.md)
  * [Developer Guide](../credential-service/developer-guide.md)

## DID-Linked Resources with Veramo SDK for cheqd

This documentation describes how you can use the Veramo SDK for cheqd to create DID-Linked Resources.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

* [Create a DID-Linked Resource](create-resource.md)
* [Create a new Resource version](create-new-version.md)
* [Create a Status List as a Resource](statuslist2021-resources.md)

## DID-Linked Resources with DID Registrar

* [Setup DID Registrar](../did-registrar/did-registrar-setup.md)
* [Create a DID-Linked Resource](../did-registrar/create-resource.md)

## DID-Linked Resources with cheqd CLI

> Note that the cheqd Cosmos CLI is not suitable for use in production environments and should only be used for testing

* [Setup cheqd CLI](../../advanced/)
* [Create a DID-Linked Resource](../../advanced/cheqd-node-cli/create-resource.md)
