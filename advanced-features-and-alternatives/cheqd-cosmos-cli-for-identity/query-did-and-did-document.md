# 📚 Querying DIDs with cheqd Cosmos CLI

## Pre-requisite

An existing `did:cheqd` entry created using any SDK/CLI. For development purposes, this can be done using [the `cheqd-node` Cosmos CLI](cheqd-cosmos-cli.md).

> NOTE: Use the [**cheqd DID Resolver**](../../guides/did-resolver.md) for production-grade usage
>
> Querying using the Cosmos CLI is useful for development purposes, but the [DID Resolver](../../guides/did-resolver.md) is designed for programmatic usage. Production-grade SDKs such as [Veramo SDK for cheqd](../../guides/software-development-kits-sdks/veramo-sdk-for-cheqd/) also rely on the DID Resolver package.

## Querying DIDDocs for existing DIDs

### Command

```bash
cheqd-noded query cheqd did <id> --chain-id <chain-id> --node <node-rpc-endpoint>
```

### Example

```bash
cheqd-noded query cheqd did did:cheqd:testnet:zJ5EDiiiKWDyo79n --chain-id cheqd-testnet-4 --node http://rpc.testnet.cheqd.network:26657
```
