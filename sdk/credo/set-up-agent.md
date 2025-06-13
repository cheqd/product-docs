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

## OpenID4VC Setup

The OpenID4VC module provides support for the [OpenID for Verifiable Credentials group of protocols](https://openid.net/sg/openid4vc/) defined under the OpenID Foundation. Currently this includes the [OpenID for Verifiable Credential Issuance](https://openid.net/specs/openid-4-verifiable-credential-issuance-1_0.html), [Self-Issued OpenID Provider v2](https://openid.net/specs/openid-connect-self-issued-v2-1_0.html), and [OpenID for Verifiable Presentations](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html).

The OpenID4VC Module in Credo currently exposes three modules, one for each role in the triangle trust: `OpenId4VcIssuerModule`, `OpenId4VcHolderModule`, and `OpenId4VcVerifierModule`. The issuer and verifier modules are expected to run in a cloud environment, as they require several endpoints to be exposed to the public internet. The holder module can run in a cloud environment or on a mobile device.

### Installing OpenID4VC Modile

To enable OpenID4VC in your agent, install the required package:

```bash
yarn add @credo-ts/openid4vc
# or
npm install @credo-ts/openid4vc
```

### Adding OpenID4VC Modules to the Agent <a href="#adding-openid4vc-modules-to-the-agent" id="adding-openid4vc-modules-to-the-agent"></a>

#### **Issuer and Verifier**[**​**](https://credo.js.org/guides/getting-started/set-up/openid4vc#issuer-and-verifier)

To issue or verify credentials using OpenID for Verifiable Credentials, include the `OpenId4VcIssuerModule` and `OpenId4VcVerifierModule`. These modules function only on the server (Node.js) and aren't compatible with React Native. They can be added individually; using both is optional. You can integrate the issuer and verifier modules with the holder module setup below to facilitate issuance, holding, and verification in OpenID4VC flows within a single agent.

We have yet to implement the `credentialRequestToCredentialMapper` method for the issuer module. Refer to the [OpenID4VC Guides](https://credo.js.org/guides/tutorials/openid4vc) for more information.

```typescript
import { Agent } from '@credo-ts/core'
// OpenID4VC issuer and verifier modules only work in Node.js
import { agentDependencies } from '@credo-ts/node'

import express, { Router } from 'express'
import { OpenId4VcIssuerModule, OpenId4VcVerifierModule } from '@credo-ts/openid4vc'

// Create two express routers, all endpoints for the
// issuer and verifier will be added to these routers
const verifierRouter = Router()
const issuerRouter = Router()

// Register the routers on the express server. The path should match
// with the baseUrl you configure in the modules below.
const app = express()
app.use('/oid4vci', issuerRouter)
app.use('/siop', verifierRouter)

const issuer = new Agent({
  config,
  dependencies: agentDependencies,
  modules: {
    openId4VcIssuer: new OpenId4VcIssuerModule({
      baseUrl: 'http://127.0.0.1:3000/oid4vci',

      // If no router is passed, one will be created.
      // you still have to register the router on your express server
      // but you can access it on agent.modules.openId4VcIssuer.config.router
      // It works the same for verifier: agent.modules.openId4VcVerifier.config.router
      router: issuerRouter,

      // Each of the endpoints can have configuration associated with it, such as the
      // path (under the baseUrl) to use for the endpoints.
      endpoints: {
        // The credentialRequestToCredentialMapper is the only required endpoint
        // configuration that must be provided. This method is called whenever a
        // credential request has been received for an offer we created. The callback should
        // return the issued credential to return in the credential response to the holder.
        credential: {
          credentialRequestToCredentialMapper: async () => {
            throw new Error('Not implemented')
          },
        },
      },
    }),

    // openId4VcVerifier module can only be used in Node.js
    openId4VcVerifier: new OpenId4VcVerifierModule({
      baseUrl: 'http://127.0.0.1:3000/siop',
      router: verifierRouter,
    }),
  },
})

// listen on port 3000 for the openid4vc app.
app.listen(3000)

```

#### Holder[​](https://credo.js.org/guides/getting-started/set-up/openid4vc#holder) <a href="#holder" id="holder"></a>

To use OpenID for Verifiable Credentials and manage credentials, incorporate the `OpenId4VcHolderModule`. This module supports both Node.js and React Native environments.

```typescript
import { Agent } from '@credo-ts/core'
// or import from '@credo-ts/react-native' for React Native
import { agentDependencies } from '@credo-ts/node'

import { OpenId4VcHolderModule } from '@credo-ts/openid4vc'

const holder = new Agent({
  config,
  dependencies: agentDependencies,
  modules: {
    // no configuration required for holder module
    openId4VcHolderModule: new OpenId4VcHolderModule(),
  },
})
```

## Next steps

Now that your Credo agent is successfully set up to work with cheqd, try following our tutorials for creating a new DID or issuing Verifiable Credentials.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create an Issuer DID with the <code>did:cheqd</code>  DID method using Credo.</td><td><a href="dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential (AnonCreds) using Credo signed by a <code>did:cheqd</code> DID.</td><td><a href="credentials/issue-credential.md">issue-credential.md</a></td></tr></tbody></table>
