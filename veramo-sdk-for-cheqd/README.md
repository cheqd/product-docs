# Veramo SDK for cheqd DID

## ℹ️ Overview

[Veramo SDK](https://veramo.io/) is a modular and pluggable client app SDK for decentralised identity and SSI applications. Our Veramo plugin - the [`@cheqd/did-provider-cheqd` NPM package](https://www.npmjs.com/package/@cheqd/did-provider-cheqd) - enables developers to interact with the cheqd ledger using Veramo SDK.

This package includes [Veramo SDK Agent methods](https://veramo.io/docs/veramo_agent/plugins) for use with the [Veramo CLI NPM package](https://www.npmjs.com/package/@veramo/cli). It can also be consumed as an NPM package outside Veramo CLI for building your own applications with NPM.

The package's core functionality is borrowed from [Veramo Core NPM package](https://www.npmjs.com/package/@veramo/core) and extends this to include cheqd ledger functionality, such as creating and managing DIDs.

### 🆔 `did:cheqd`-specific functionality

`did-provider-cheqd` is the first Veramo SDK plug-in that utilises the *DID Manager Update* method to offer a full-body DIDDoc update for a DID on cheqd ledger, rather than individual field update transactions used more commonly in other DID methods such as [`did:ethr`](https://developer.uport.me/ethr-did/docs/index).

New DID creation can also be done by passing a full-body DIDoc payload in JSON, rather than having to assemble the document field-by-field.

## 📝 Architecture

![Veramo SDK for cheqd architecture](../.gitbook/assets/veramo-sdk-for-cheqd-arch.png)

*Figure 1: Veramo SDK for cheqd architecture with components ([editable Draw.io version](../.gitbook/assets/veramo-sdk-for-cheqd.drawio))*

`did-provider-cheqd` consumes functionality that exists within the [`@cheqd/sdk` NPM package](https://www.npmjs.com/package/@cheqd/sdk) in a way that complies to the Veramo `AbstractIdentifierProvider`. (You don't need to call this package separately as it's already included as a dependency in `package.json`.) The `@cheqd/sdk` package adds Cosmos SDK specific functionality such as signing transactions, paying for ledger fees, etc.

For identity-related keys, this plugin uses [Veramo's Key Management System (KMS)](https://www.npmjs.com/package/@veramo/key-manager) to manage keys for create and update operations.

This package works alongside other base Veramo packages:

* [`@veramo/core`](https://www.npmjs.com/package/@veramo/core)
* [`@veramo/cli`](https://www.npmjs.com/package/@veramo/cli)
* [`@veramo/credential-w3c`](https://www.npmjs.com/package/@veramo/credential-w3c)

Find out about [other Veramo plug-ins in their documentation](https://veramo.io/docs/veramo_agent/plugins/).
