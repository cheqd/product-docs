# Developing custom apps that use identity transactions on `cheqd-node`

This document describes the possible ways to use [cheqd product identity features](https://product.cheqd.io/).

> This is an **advanced** guide for those who want to develop applications that directly interact with the ledger code.
>
> Most developers should start with our pre-built SDKs such as [Veramo SDK for cheqd](../veramo-sdk-for-cheqd/README.md) or the cheqd-node Cosmos CLI.

## Overview

The [ledger code package `cheqd-node`](https://github.com/cheqd/cheqd-node) is based on [Cosmos SDK](https://docs.cosmos.network/) and uses the same module-based approach that is does.

There are two custom modules on cheqd ledger related to identity functionality:

1. `cheqd` module, for everything related to [Decentralized Identifiers (DIDs)](README.md)
2. `resource` module, for [Resources on-ledger](../resources/README.md)

## Developing Golang apps for cheqd

If your application is Golang-based, it's extremely easy to [consume `cheqd-node` as a Golang module](https://github.com/cheqd/cheqd-node).

### Encoding & Utilities

Just as in Cosmos SDK, cheqd-node uses Protobufs to encode and exchange information. These can be found in the [`proto` directory on `cheqd-node`](https://github.com/cheqd/cheqd-node/tree/main/proto/) repository.

Generated/built Protobuf for repeatable builds are also published to the [`cheqd` repository on Buf.build](https://buf.build/cheqd/repositories).

Types and utilities are available for re-use for each module as well. The `utils` include functions for working with these custom modules, validation logic, etc.

* `cheqd` (DID) module
  * [`x/cheqd/types`](https://github.com/cheqd/cheqd-node/tree/main/x/cheqd/types)
  * [`x/cheqd/utils`](https://github.com/cheqd/cheqd-node/tree/main/x/cheqd/utils)
* `resource` module
  * [`x/resource/types`](https://github.com/cheqd/cheqd-node/tree/main/x/resource/types)
  * [`x/resource/utils`](https://github.com/cheqd/cheqd-node/tree/main/x/resource/utils)

### Sending transactions to ledger

After compiling a message (in your custom application), you can make a gRPC call to the ledger with your message.

> [**cheqd DID Resolver**](https://github.com/cheqd/did-resolver) is a real-world example of a Golang app built using `cheqd-node` primitives
>
> This allowed us to decouple DID Resolution logic from the ledger, while re-utilising existing code.

## Developing in other languages for cheqd

In general, the process building applications that work with cheqd in other languages follows very similar logic as described above for Golang apps.

> Example 1: [**Veramo SDK for cheqd**](../../veramo-sdk-for-cheqd/README.md)
>
> **Veramo SDK for cheqd** combines the base NPM packages mentioned above to integrate cheqd ledger functionality into the 3rd party [Veramo SDK](https://veramo.io).

The high-level steps are:

1. **Consume Protobofs for cheqd**: Our [Buf.build packages for cheqd](https://github.com/cheqd/did-resolver) can make this take simpler for many languages. *A real-world example of this is the [`@cheqd/ts-proto`](https://github.com/cheqd/ts-proto) TypeScript NPM package.*
2. **Prepare and sign the message correctly**: This relies partly on understanding general Cosmos SDK message format and transaction signing, as well as any cheqd-specific bits. *A real-world example of this is the [`@cheqd/sdk`](https://github.com/cheqd/sdk) NPM package that consumes `@cheqd/ts-proto` NPM package.*
3. **Send the message to ledger using gRPC/RPC**: Once a message/transaction is correctly compiled and signed, standard transport libraries can be used to target the [gRPC/RPC endpoints](https://docs.cosmos.network/master/core/grpc_rest.html) for a `cheqd-node` instance.

> Example 2: [**Everynym VDR Tools**](vdr-tools-with-cheqd.md)
>
> [**Evernym's VDR Tools SDK**](https://gitlab.com/evernym/verity/vdr-tools/-/tree/main/libvdrtools/src/services/cheqd_ledger) is an independent implementation written in Rust that [integrates some cheqd ledger functionality](vdr-tools-with-cheqd.md).
