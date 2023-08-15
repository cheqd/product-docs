# Querying DIDs with cheqd Cosmos CLI

## Pre-requisite

An existing `did:cheqd` entry created using any SDK/CLI. For development purposes, this can be done using [the `cheqd-node` Cosmos CLI](create-did.md).

> NOTE: Use the [**cheqd DID Resolver**](../../did-resolver/) for production-grade usage
>
> Querying using the Cosmos CLI is useful for development purposes, but the [DID Resolver](../../did-resolver/) is designed for programmatic usage. Production-grade SDKs such as [Veramo SDK for cheqd](../../../sdk/veramo-plugin/) also rely on the DID Resolver package.

## Querying DIDDocs for existing DIDs

### Command

```bash
cheqd-noded query cheqd did-document <id> --chain-id <chain-id> --node <node-rpc-endpoint>
```

### Example

```bash
cheqd-noded query cheqd did-document did:cheqd:mainnet:7urFZAL6hvyfHWjfEmEpnN --node https://rpc.cheqd.net:443
```
