---
description: >-
  Issue a SD-JWT Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using Credo and OpenID4VCI.
---

# Issue a Verifiable Credential

Using the OpenID4VC module, you can issue OpenID compatible SD-JWT Credentials signed by a `did:cheqd` identifier. This guide walks through the full flow using the **Credo Agent**.

## Prerequisites

Before you being, ensire you have:

* A [Credo Agent](../../set-up-agent.md) configured with:
  * `@credo-ts/cheqd` for DID operations and resource publishing.
  * `@credo-ts/didcomm` for DIDComm messaging.
  * `@credo-ts/openid4vc` for openID4vc modules.
* Two agents: [an **Issuer**](../../set-up-agent.md#issuer-and-verifier) and [a **Holder**](../../set-up-agent.md#holder) (can be separate apps or run locally).
* Secure connectivity between [agents using **Out-of-Band (OOB)**](../issue-credential.md#step-1-create-a-connection-with-holder) or a supported connection method.
* **A cheqd testnet or mainnet account** with sufficient tokens for DID operations

## Step 1: Configure the Issuer

Configure the issuer and the credentials that you want to issue.

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
```

If you want to change the display metadata or the credentials supported by the issuer, you can use the `issuer.modules.openId4VcIssuer.updateIssuer` method.

## Step 2: Create Credential Offer

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

\
