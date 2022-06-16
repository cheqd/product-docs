# Overview

This document describes the possible ways to use `cheqd` features like `create-did`, `update-did` or `query-did`. 
`cheqd-node` is based on `cosmos-sdk` and uses the same module-based approach. `cheqd`-related functionality is concentrated in the module named `cheqd` and can be used as base for building other features or can be easy injected into your application.

## Go-based application

If your application is already go-based it would be not to hard to use `cheqd-node` project as a tool for communicating with `cheqd` ledger.

### Building message

For communications between ledger (server) and client sides we are using the same approach as cosmos does, it's protobufs. They can be found here:

```link
https://github.com/cheqd/cheqd-node/tree/main/proto/cheqd/v1
```

You can also use such protobufs for searching the exact format/view of our transactions for DID-functionality. So, as result, for communicating with `cheqd` ledger you can use the next modules:

- Package cheqd types: https://github.com/cheqd/cheqd-node/tree/main/x/cheqd/types
It includes all the main types, generated due to corresponded protobufs.

- Package utils: https://github.com/cheqd/cheqd-node/tree/main/x/cheqd/utils
It includes additional utils and functions for work with DIDs, validation and compilation them.

### Sending

So, after compiling the message you can use gRPC protocol for sending such message to the ledger. For example, package from google: `google.golang.org/grpc`.
The list of endpoints can be found in a swagger config file (it's not deployed yet):

```link
https://github.com/cheqd/cheqd-node/blob/main/docs/identity-api/swagger.yaml
```

### P.S.

Real example of using `cheqd-node` as sdk can be found in `did-resolver`, especially in package:

```
https://github.com/cheqd/did-resolver/tree/main/services
```

## Other languages

In general, the process of communication between ledger and a client can be divided into the next steps:

- Prepare the message
- Send it to the ledger and process the result

So, due to this plan, in case of using other language, not goland, all you need is:
- Make it possible to generate the code from protobufs.
- Use such classes/interfaces/objects for building a message
- Use gRPC or just RPC interfaces for sending the message. Just for RPC the address like <node-ip>:26657 is used by default.
- Process the result by using also the same objects from protobufs

For example, there is a project `libvdrtools` which is written on Rust and using our protobufs for communicating with ledger.
Here is the URL:

```link
https://gitlab.com/evernym/verity/vdr-tools/-/tree/main/libvdrtools/src/services/cheqd_ledger
```
