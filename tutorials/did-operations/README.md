# 🆔 Decentralised Identifiers (DIDs)

> **Learn about DIDs** If you want to learn about what [DIDs are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-decentralised-identifier-did)

## Credential Service

The easiest way to create Decentralized Identifiers (DIDs) on cheqd is with our "Credentials-as-a-Service" offering - a set of hosted APIs which can create DIDs, DID-Linked Resources and issue Credentials in a few clicks or minimal lines of code.

Currently, Credential Service supports both JSON (JWT) and JSON-LD Credentials.&#x20;

See the links below to get started:

* [Credential Service](../credential-service/)
  * [User Guide](../credential-service/user-guide.md)
  * [Developer Guide](../credential-service/developer-guide.md)

## DIDs with Veramo SDK for cheqd

This documentation describes how you can use the Veramo SDK for cheqd to create, manage and update Decentralised Identifiers (DIDs).

> ⚠️ **Before you begin...**
>
> Make sure you've correctly [configured the cheqd plugin's agent settings](../../guides/sdk/veramo-sdk-for-cheqd/setup.md) for Veramo CLI

* [Create a DID](create-did.md)
* [Querying a DID](query-did.md)
* [Update an existing DID](update-did.md)
* [Deactivate a DID](deactivate-did.md)
* [Create an off-ledger holder DID](create-subject-did.md)
* [Manage identity keys](identity-keys.md)
* [DID Operations Troubleshooting](troubleshooting.md)

## DIDs with DID Registrar

* [Setup DID Registrar](../did-registrar/did-registrar-setup.md)
* [Create a DID](../did-registrar/create-did.md)

## DIDs with cheqd CLI

> Note that the cheqd Cosmos CLI is not suitable for use in production environments and should only be used for testing

* [Setup cheqd CLI](../../advanced/)
* [Create a DID](../../advanced/cheqd-node-cli/create-did.md)
* [Query a DID](../../advanced/cheqd-node-cli/query-did.md)
* [Update a DID](../../advanced/cheqd-node-cli/update-did.md)
* [Deactivate a DID](../../advanced/cheqd-node-cli/deactivate-did.md)
