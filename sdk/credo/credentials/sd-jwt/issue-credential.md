---
description: >-
  Issue a SD-JWT Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using Credo and OpenID4VCI.
---

# Issue a Verifiable Credential

Using the OpenID4VC module, you can issue OpenID compatible SD-JWT Credentials signed by a `did:cheqd` identifier. This guide walks through the flow of issuance using the **Credo Agent**.

## Prerequisites

Before you being, ensure you have:

* Basic knowledge of [Credo Agent configuration](https://docs.cheqd.io/product/sdk/credo/set-up-agent).
* **A cheqd testnet or mainnet account** with sufficient tokens for DID operations.

## Step 1: Install dependencies

```bash
npm install @credo-ts/core @credo-ts/node @credo-ts/cheqd
npm install @credo-ts/openid4vc
```

## Step 2: Configure the Issuer Agent <a href="#step-2-set-up-the-issuer-agent" id="step-2-set-up-the-issuer-agent"></a>

Configure the issuer with cheqd and OpenID4VC Modules

```typescript
import { Agent, DidsModule, KeyType } from '@credo-ts/core';
import { agentDependencies } from '@credo-ts/node';
import { CheqdModule } from '@credo-ts/cheqd';
import express, { Router } from 'express'
import { OpenId4VcIssuerModule, OpenId4VcVerifierModule } from '@credo-ts/openid4vc';

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
    dids: new DidsModule({
      registrars: [new CheqdDidRegistrar()],
      resolvers: [new CheqdDidResolver()],
    }),
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
    openId4VcIssuer: new OpenId4VcIssuerModule({
      baseUrl: 'https://your-issuer-host/oid4vci',
      router: issuerRouter,
      endpoints: {
        // The credentialRequestToCredentialMapper is the only required endpoint
        // configuration that must be provided. This method is called whenever a
        // credential request has been received for an offer we created. The callback should
        // return the issued credential to return in the credential response to the holder.
        credential: {
          // you'll map credential once requests come in
          credentialRequestToCredentialMapper: async ({ credentialRequest }) => {
            // See step 5.
          },
        },
      },
    }),
    // openId4VcVerifier module can only be used in Node.js
    openId4VcVerifier: new OpenId4VcVerifierModule({
      baseUrl: 'https://your-issuer-host/siop',
      router: verifierRouter,
    }),
  },
});
// listen on port 3000 for the openid4vc app.
app.listen(3000)
```

This registers:

* `cheqd` for did:cheqd DID creation and ledger integration
* `openId4VcIssuer` and `openId4VcVerifier`  for OID4VC flows

## Step 3: Create a cheqd DID and link it to SD‑JWT issuance

```typescript
// Create a did:cheqd that we will use for issuance
const issuerDidResult = await issuer.dids.create({
  method: 'cheqd',
  options: {
    network: 'testnet',
    methodSpecificIdAlgo: 'uuid',
  },
})

if (issuerDidResult.didState.state !== 'finished') {
  throw new Error('DID creation failed.')
}
const issuerDid = issuerDidResult.did;
```

## Step 4: Define the Issuer with SD‑JWT + did:cheqd support

```typescript
import { JwaSignatureAlgorithm } from '@credo-ts/core'

// Create an issuer with one supported credential: AcmeCorpEmployee
const openid4vcIssuer = await issuer.modules.openId4VcIssuer.createIssuer({
  display: [
    {
      name: 'ACME Corp.',
      description: 'ACME Corp. is a company that provides the best services.',
      text_color: '#000000',
      background_color: '#FFFFFF',
      logo: {
        url: 'https://acme.com/logo.png',
        alt_text: 'ACME Corp. logo',
      },
    },
  ],
  credentialsSupported: [
    {
      format: 'vc+sd-jwt',
      vct: 'AcmeCorpEmployee',
      id: 'AcmeCorpEmployee',
      cryptographic_binding_methods_supported: ['did:cheqd'],
      cryptographic_suites_supported: [JwaSignatureAlgorithm.ES256],
    },
  ],
})
```

This declares support for:

* `vc+sd-jwt` format
* cryptographic binding with wasm-bound `did:cheqd` keys

If you want to change the display metadata or the credentials supported by the issuer, you can use the `issuer.modules.openId4VcIssuer.updateIssuer` method.

## Step 5: Map credential requests to SD‑JWT payloads

Implement the mapper, triggered on issuance:

```ts
const credentialRequestToCredentialMapper: OpenId4VciCredentialRequestToCredentialMapper = async ({
  // agent context for the current wallet / tenant
  agentContext,
  // the credential offer related to the credential request
  credentialOffer,
  // the received credential request
  credentialRequest,
  // the list of credentialsSupported entries
  credentialsSupported,
  // the cryptographic binding provided by the holder in the credential request proof
  holderBinding,
  // the issuance session associated with the credential request and offer
  issuanceSession,
}) => {
  const firstSupported = credentialsSupported[0]
  const { sub } = credentialRequest.claims;
  const payload = {
    vct: firstSupported.vct,
    firstName: 'John',
    lastName: 'Doe',
    employeeId: 'EMP-1234',
    role: 'engineer',
    issuedAt: Math.floor(Date.now() / 1000),
  };
  return {
    credentialSupportedId: firstSupported.id,
    format: 'vc+sd-jwt',
    // We can provide the holderBinding as is, if we don't want to make changes
    holder: holderBinding,
    payload: payload,
    disclosureFrame: {
      _sd: ['lastName'],
    },
    issuer: {
      method: 'cheqd',
      issuerDid,
    },
  };
}
```

This constructs a standard SD‑JWT payload—structural claims ready for selective disclosure.

## Step 6: Create Credential Offer

Once you have configured the issuer, you can create a credential offer. The credential offer method will generate an offer URI that you can share with a holder.

```typescript
const { credentialOffer, issuanceSession } = 
  await issuer.modules.openId4VcIssuer.createCredentialOffer({
    issuerId: openid4vcIssuer.issuerId,
    // values must match the `id` of the credential supported by the issuer
    offeredCredentials: ['AcmeCorpEmployee'],
  
    // Only pre-authorized code flow is supported
    preAuthorizedCodeFlowConfig: {
      userPinRequired: false,
    },
  
    // You can store any metadata about the issuance here
    issuanceMetadata: {
      someKey: 'someValue',
    },
})

// Listen and react to changes in the issuance session
issuer.events.on<OpenId4VcIssuanceSessionStateChangedEvent>(
  OpenId4VcIssuerEvents.IssuanceSessionStateChanged,
  (event) => {
    if (event.payload.issuanceSession.id === issuanceSession.id) {
      console.log('Issuance session state changed to ', 
        event.payload.issuanceSession.state)
    }
  }
)
```

We have also added an event listener that listens for state changed events, this allows Issuer to know when the issuance session is complete.\
