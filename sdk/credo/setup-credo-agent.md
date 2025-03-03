# Setup Credo Agent

## Pre-requisites

* [Node.js](https://nodejs.org/) - v16 or v18
* [yarn](https://classic.yarnpkg.com/lang/en/docs/install) or [npm](https://www.npmjs.com/)
* Node.js or React Native project.

For more details, check [Credo Getting Started](https://credo.js.org/guides/getting-started) guide.

## Installing Cheqd

When using cheqd method with Credo, there are a few extra dependencies that need to be installed. We need to install the `@credo-ts/cheqd` package, which implements the needed interfaces for the agent.

#### **React Native**[**​**](https://credo.js.org/guides/getting-started/set-up/cheqd#react-native)

To enable react-native support we need to follow the steps below:

In the `package.json` file add the below code snippet, which replaces the cosmjs dependencies with the cosmjs-rn packages.

Using [NPM `overrides`](https://docs.npmjs.com/cli/v9/configuring-npm/package-json#overrides)  or  [Yarn `resolutions`](https://classic.yarnpkg.com/lang/en/docs/selective-version-resolutions/) we can point the `cosmjs` packages to `cosmjs-rn`.

{% tabs %}
{% tab title="NPM" %}
```javascript
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
{% endtab %}

{% tab title="Yarn" %}
```javascript
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
{% endtab %}
{% endtabs %}

After that, we need to add a buffer polyfill

```bash
yarn add buffer
```

Then, create a shim.js file with the below code snippet

```javascript
import { Buffer } from 'buffer'
global.Buffer = Buffer
```

`import shim.js` file into your file where the App is imported.

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

The `cosmosPayerSeed` can be a 32-bit seed value or mnemonic. It can be managed using the Keplr wallet, which is available as a mobile app or browser extension for Chrome and Safari. Keplr enables users to create accounts, exchange tokens, etc. To setup Keplr wallet for cheqd follow this [tutorial](https://learn.cheqd.io/getting-set-up-on-cheqd/cheqd-supported-wallets/keplr-wallet).

## Next steps

Now that your Credo agent is successfully set up to work with cheqd, try following our tutorials for creating a new DID or issuing Verifiable Credentials.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create an Issuer DID using the did:cheqd DID method.</td><td><a href="decentralized-identifiers-dids/create-a-did.md">create-a-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential using Credo signed by a cheqd DID.</td><td><a href="verifiable-credentials-and-presentations/issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr></tbody></table>
