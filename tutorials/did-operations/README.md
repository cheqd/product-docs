# 🆔 Decentralised Identifiers (DIDs)

> **Learn about DIDs** If you want to learn about what [DIDs are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did)

## Multiple options for creating DIDs

There are multiple supported ways to create DIDs on cheqd mainnet and testnet:

1. Enterprise SDKs
   1. [Veramo SDK for cheqd](create-a-did.md)
   2. [walt.id SSI Kit](https://docs.walt.id/v/ssikit/ecosystems/cheqd/create-did)
2. [DID Registrar](../did-registrar/create-a-did.md)
3. [cheqd CLI](../../advanced/cheqd-node-cli/create-did.md) (testing purposes only)

> Learn about [cheqd's DID method in our guide here](../../architecture/adr-list/adr-001-cheqd-did-method.md)

## DIDs with Veramo SDK for cheqd

This documentation describes how you can use the Veramo SDK for cheqd to create, manage and update Decentralised Identifiers (DIDs).

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

* [Create a DID](create-a-did.md)
* [Querying a DID](query-did.md)
* [Update an existing DID](update-did.md)
* [Deactivate a DID](deactivate-a-did.md)
* [Create an off-ledger holder DID](create-subject-did.md)
* [Manage identity keys](identity-key-handling.md)
* [DID Operations Troubleshooting](did-operations-troubleshooting.md)

## DIDs with DID Registrar

* [Setup DID Registrar](../did-registrar/did-registrar-setup.md)
* [Create a DID](../did-registrar/create-a-did.md)

## DIDs with cheqd CLI

> Note that the cheqd Cosmos CLI is not suitable for use in production environments and should only be used for testing

* [Setup cheqd CLI](../../advanced/developer-guide.md)
* [Create a DID](../../advanced/cheqd-node-cli/create-did.md)
* [Query a DID](../../advanced/cheqd-node-cli/query-did-and-did-document.md)
* [Update a DID](../../advanced/cheqd-node-cli/update-and-manage-did-document.md)
* [Deactivate a DID](../../advanced/cheqd-node-cli/deactivate-a-did.md)
