# 🔗 Create DID-Linked Resources

## Understanding DID-Linked Resources

* [Understanding DID-Linked Resources](../../guides/did-linked-resources/README.md)
* [ADR 002: DID-Linked Resources](../../architecture/adr-list/adr-002-did-linked-resources.md)

## Multiple options for creating DID-Linked Resources

There are multiple supported ways to create DID-Linked Resources on cheqd mainnet and testnet:

1. Enterprise SDKs
   1. [Veramo SDK for cheqd](#did-linked-resources-with-veramo-sdk-for-cheqd)
2. [DID Registrar](#did-linked-resources-with-did-registrar)
3. [cheqd CLI](#did-linked-resources-with-cheqd-cli) (testing purposes only)

## DID-Linked Resources with Veramo SDK for cheqd

This documentation describes how you can use the Veramo SDK for cheqd to create DID-Linked Resources.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

* [Create a DID-Linked Resource](create-a-resource.md)
* [Create a new Resource version](create-a-new-resource-version.md)
* [Create a Status List as a Resource](../../guides/sdk/veramo-sdk-for-cheqd/statuslist2021-resources.md)

## DID-Linked Resources with DID Registrar

* [Setup DID Registrar](../did-registrar/did-registrar-setup.md)
* [Create a DID-Linked Resource](../did-registrar/create-a-resource.md)

## DID-Linked Resources with cheqd CLI

> Note that the cheqd Cosmos CLI is not suitable for use in production environments and should only be used for testing

* [Setup cheqd CLI](../../advanced/README.md)
* [Create a DID-Linked Resource](../../advanced/cheqd-node-cli/create-resource.md)
