---
description: Setup your Credo Agent to get started.
---

# Setup Credo Agent

This guide walks you through the setup process for a **Credo Agent** with support for **cheqd DIDs** and **AnonCreds**.

## Prerequisites

Before you begin, make sure you have the following:

* **Node.js** v16 or v18
* **Yarn** or **npm** installed
* A working **Node.js** or **React Native** project
* Follow the Getting Started guide if you're new to Credo

## Installing Cheqd

To enable cheqd in your agent, install the required package:

```bash
yarn add @credo-ts/cheqd
# or
npm install @credo-ts/cheqd
```

This package provides everything needed to register, resolve, and interact with `did:cheqd` identifiers and AnonCreds objects.

### React Native Support

If you're using **React Native**, additional steps are needed to support `cosmjs`:

#### 1. Use `cosmjs-rn` packages

Update your `package.json` using **npm overrides** or **Yarn resolutions**:

**Example (NPM `overrides`):**

```json
{
  "overrides": {
    "@cosmjs/amino": "npm:@cosmjs-rn/amino@^0.27.1",
    "@cosmjs/encoding": "npm:@cosmjs-rn/encoding@^0.27.1",
    "@cosmjs/math": "npm:@cosmjs-rn/math@^0.27.1",
    "@cosmjs/stargate": "npm:@cosmjs-rn/stargate@^0.27.1",
    "@cosmjs/tendermint-rpc": "npm:@cosmjs-rn/tendermint-rpc@^0.27.1",
    "@cosmjs/utils": "npm:@cosmjs-rn/utils@^0.27.1",
    "@cosmjs/proto-signing": "npm:@cosmjs-rn/proto-signing@^0.27.1",
    "@cosmjs/crypto": "npm:@cosmjs-rn/crypto@^0.27.1"
  }
}
```

**Example (Yarn `resolutions`):**

```json
{
  "resolutions": {
    "@cosmjs/amino": "npm:@cosmjs-rn/amino@^0.27.1",
    "@cosmjs/encoding": "npm:@cosmjs-rn/encoding@^0.27.1",
    "@cosmjs/math": "npm:@cosmjs-rn/math@^0.27.1",
    "@cosmjs/stargate": "npm:@cosmjs-rn/stargate@^0.27.1",
    "@cosmjs/tendermint-rpc": "npm:@cosmjs-rn/tendermint-rpc@^0.27.1",
    "@cosmjs/utils": "npm:@cosmjs-rn/utils@^0.27.1",
    "@cosmjs/proto-signing": "npm:@cosmjs-rn/proto-signing@^0.27.1",
    "@cosmjs/crypto": "npm:@cosmjs-rn/crypto@^0.27.1"
  }
}
```

#### 2. Add Buffer Polyfill

Install the `buffer` package:

```bash
bashCopyEdityarn add buffer
```

Create a `shim.js` file:

```ts
tsCopyEditimport { Buffer } from 'buffer'
global.Buffer = Buffer
```

Then, import this shim in your entry point (e.g. before your `App.tsx` is rendered):

```ts
tsCopyEditimport './shim'
```

### Adding cheqd to the Agent[​](https://credo.js.org/guides/getting-started/set-up/cheqd#adding-the-cheqd-to-the-agent) <a href="#adding-the-cheqd-to-the-agent" id="adding-the-cheqd-to-the-agent"></a>

After installing the dependencies, we can register the cheqd module on the agent by adding the below code snippet to the agent constructor.

```typescript
import { Agent, DidsModule, KeyType, DidDocument } from '@credo-ts/core'
import { agentDependencies } from '@credo-ts/node'
import { AskarModule } from '@credo-ts/askar'
import { askar } from '@openwallet-foundation/askar-nodejs'
import {
  ConnectionsModule,
  V2ProofProtocol,
  V2CredentialProtocol,
  ProofsModule,
  AutoAcceptProof,
  AutoAcceptCredential,
  CredentialsModule,
  HttpOutboundTransport,
  getDefaultDidcommModules,
} from '@credo-ts/didcomm'
import {
  CheqdAnonCredsRegistry,
  CheqdDidRegistrar,
  CheqdDidResolver,
  CheqdModule,
  CheqdModuleConfig,
} from '@credo-ts/cheqd'
import { AnonCredsModule } from '@credo-ts/anoncreds'
import { anoncreds } from '@hyperledger/anoncreds-nodejs'

const agent = new Agent({
  config,
  dependencies: agentDependencies,
  modules: {
    dids: new DidsModule({
      registrars: [new CheqdDidRegistrar()],
      resolvers: [new CheqdDidResolver()],
    }),

    // AnonCreds
    anoncreds: new AnonCredsModule({
      registries: [new CheqdAnonCredsRegistry()],
      anoncreds,
    }),

    // Add cheqd module
    cheqd: new CheqdModule(
      new CheqdModuleConfig({
        networks: [
          {
            network: '<mainnet or testnet>',
            cosmosPayerSeed: '<cosmos payer seed or mnemonic>',
          },
        ],
      })
    ),
    // Indy VDR can optionally be used with Askar as wallet and storage implementation
    askar: new AskarModule({
      askar,
    }),
    connections: new ConnectionsModule({
      autoAcceptConnections: true,
    }),
    credentials: new CredentialsModule({
      autoAcceptCredentials: AutoAcceptCredential.ContentApproved,
      credentialProtocols: [
        new V2CredentialProtocol({
          credentialFormats: [new LegacyIndyCredentialFormatService(), new AnonCredsCredentialFormatService()],
        }),
      ],
    }),
  },
})
```

The `cosmosPayerSeed` can be a 32-bit seed value or mnemonic. It can be managed using Leap wallet, which is available as a mobile app or browser extension for Chrome and Safari. Leap enables users to create accounts, exchange tokens, etc. To setup Leap wallet for cheqd follow the tutorial below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Leap Wallet</strong></mark></td><td>Manage your CHEQ natively through the Leap Wallet with full support on desktop, browser and mobile.</td><td><a href="../../.gitbook/assets/leap wallet.png">leap wallet.png</a></td><td><a href="../../network/wallets/setup-leap-wallet/">setup-leap-wallet</a></td></tr></tbody></table>

## Next steps

Now that your Credo agent is successfully set up to work with cheqd, try following our tutorials for creating a new DID or issuing Verifiable Credentials.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create an Issuer DID with the <code>did:cheqd</code>  DID method using Credo.</td><td><a href="dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential (AnonCreds) using Credo signed by a <code>did:cheqd</code> DID.</td><td><a href="credentials/issue-credential.md">issue-credential.md</a></td></tr></tbody></table>
