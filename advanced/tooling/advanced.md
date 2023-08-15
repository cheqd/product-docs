# Direct interaction with ledger code

> This is an **advanced** guide for those who want to develop applications that directly interact with the ledger code.
>
> Most developers should start with our pre-built SDKs such as [Veramo SDK for cheqd](../../sdk/veramo-plugin/) or the cheqd-node Cosmos CLI.

## Overview

The [ledger code package `cheqd-node`](https://github.com/cheqd/cheqd-node) is based on [Cosmos SDK](https://docs.cosmos.network/) and uses the same module-based approach that is does.

There are two custom modules on cheqd ledger related to identity functionality:

1. `cheqd` module, for everything related to Decentralized Identifiers (DIDs)
2. `resource` module, for [DID-Linked Resources](../../credential-service/did-linked-resources/understanding-dlrs/)

## Developing Golang apps for cheqd

If your application is Golang-based, it's extremely easy to [consume `cheqd-node` as a Golang module](https://github.com/cheqd/cheqd-node).

### Encoding & Utilities

Just as in Cosmos SDK, cheqd-node uses Protobufs to encode and exchange information. These can be found in the [`proto` directory on `cheqd-node`](https://github.com/cheqd/cheqd-node/tree/main/proto/) repository.

Generated/built Protobuf for repeatable builds are also published to the [`cheqd` repository on Buf.build](https://buf.build/cheqd/repositories).

Types and utilities are available for re-use for each module as well. The `utils` include functions for working with these custom modules, validation logic, etc.

* `cheqd` (DID) module
  * [`x/cheqd/types`](https://github.com/cheqd/cheqd-node/tree/main)
  * [`x/cheqd/utils`](https://github.com/cheqd/cheqd-node/tree/main)
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

> Example 1: [**Veramo SDK for cheqd**](../../sdk/veramo-plugin/)
>
> **Veramo SDK for cheqd** combines the base NPM packages mentioned above to integrate cheqd ledger functionality into the 3rd party [Veramo SDK](https://veramo.io).

The high-level steps are:

1. **Consume Protobofs for cheqd**: Our [Buf.build packages for cheqd](https://github.com/cheqd/did-resolver) can make this take simpler for many languages. _A real-world example of this is the_ [_`@cheqd/ts-proto`_](https://github.com/cheqd/ts-proto) _TypeScript NPM package._
2. **Prepare and sign the message correctly**: This relies partly on understanding general Cosmos SDK message format and transaction signing, as well as any cheqd-specific bits. _A real-world example of this is the_ [_`@cheqd/sdk`_](https://github.com/cheqd/sdk) _NPM package that consumes `@cheqd/ts-proto` NPM package._
3. **Send the message to ledger using gRPC/RPC**: Once a message/transaction is correctly compiled and signed, standard transport libraries can be used to target the [gRPC/RPC endpoints](https://docs.cosmos.network/main/core/grpc\_rest.html) for a `cheqd-node` instance.

> Example 2: [**Evernym VDR Tools**](vdr-tools.md)
>
> [**Evernym's VDR Tools SDK**](https://gitlab.com/evernym/verity/vdr-tools/-/tree/main/libvdrtools/src/services/cheqd\_ledger) is an independent implementation written in Rust that [integrates some cheqd ledger functionality](vdr-tools.md).
