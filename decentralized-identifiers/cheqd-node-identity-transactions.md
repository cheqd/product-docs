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

After compiling a message (), you can use gRPC protocol for sending such message to the ledger. For example, package from google: `google.golang.org/grpc`.
The list of endpoints can be found in a swagger config file (it's not deployed yet):

```link
https://github.com/cheqd/cheqd-node/blob/main/docs/identity-api/swagger.yaml
```

### P.S

Real example of using `cheqd-node` as sdk can be found in `did-resolver`, especially in package:

```link
https://github.com/cheqd/did-resolver/tree/main/services
```

## Other languages

In general, the process of communication between ledger and a client can be divided into the next steps:

- Prepare the message
- Send it to the ledger and process the result

So, due to this plan, in case of using other language, not goland, all you need is:

- Make it possible to generate the code from protobufs.
- Use such classes/interfaces/objects for building a message
- Use gRPC or just RPC interfaces for sending the message. Just for RPC the address like [node-ip]:26657 is used by default.
- Process the result by using also the same objects from protobufs

For example, there is a project `libvdrtools` which is written on Rust and using our protobufs for communicating with ledger.
Here is the URL:

```link
https://gitlab.com/evernym/verity/vdr-tools/-/tree/main/libvdrtools/src/services/cheqd_ledger
```
