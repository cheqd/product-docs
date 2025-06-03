---
description: >-
  Issue a JSON-LD Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using Credo.
---

# Issue a Verifiable Credential

Using the **Issue Credential v2 Protocol**, you can issue JSON-LD Verifiable Credentials signed by a **`did:cheqd`** identifier with just a few lines of code. This guide walks through the full flow using the **Credo Agent**.

## Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Before you begin, ensure you have:

* Basic knowledge of [Credo Agent configuration](../../set-up-agent.md).
* **A cheqd testnet or mainnet account** with sufficient tokens for DID operations

## Step 1: Install dependencies

```bash
npm install @credo-ts/core @credo-ts/node @credo-ts/askar @credo-ts/cheqd
npm install @hyperledger/aries-askar-nodejs
```

## Step 2: Set up the Issuer Agent

The issuer agent requires the cheqd module for DID operations and additional modules for W3C JSON-LD credential processing.

{% code title="Issuer" %}
```typescript
import type { InitConfig } from '@credo-ts/core'
import { AskarModule } from '@credo-ts/askar'
import {
  Agent,
  CredentialsModule,
  V2CredentialProtocol,
  JsonLdCredentialFormatService,
  DidsModule,
  HttpOutboundTransport,
  WsOutboundTransport,
  ProofsModule,
  V2ProofProtocol,
  DifPresentationExchangeProofFormatService,
  CacheModule,
  InMemoryLruCache,
  W3cCredentialsModule,
  KeyType,
  DidDocumentBuilder,
  utils,
  getEd25519VerificationKey2018,
} from '@credo-ts/core'
import { agentDependencies, HttpInboundTransport } from '@credo-ts/node'
import { ariesAskar } from '@hyperledger/aries-askar-nodejs'
import { CheqdModule, CheqdModuleConfig, CheqdDidRegistrar, CheqdDidResolver } from '@credo-ts/cheqd'

let issuerDid: string

const issuerConfig: InitConfig = {
  label: 'cheqd-jsonld-issuer',
  walletConfig: {
    id: 'cheqd-issuer-wallet',
    key: 'testkey0000000000000000000000000',
  },
}

const initializeIssuerAgent = async () => {
  const issuer = new Agent({
    config: issuerConfig,
    dependencies: agentDependencies,
    modules: {
      askar: new AskarModule({ ariesAskar }),
      dids: new DidsModule({
        registrars: [new CheqdDidRegistrar()],
        resolvers: [new CheqdDidResolver()],
      }),
      cheqd: new CheqdModule(
        new CheqdModuleConfig({
          networks: [
            {
              network: 'testnet', // or 'mainnet'
              cosmosPayerSeed: 'your-cosmos-payer-seed-here',
            },
          ],
        })
      ),
      credentials: new CredentialsModule({
        credentialProtocols: [
          new V2CredentialProtocol({
            credentialFormats: [new JsonLdCredentialFormatService()],
          }),
        ],
      }),
      proofs: new ProofsModule({
        proofProtocols: [
          new V2ProofProtocol({
            proofFormats: [new DifPresentationExchangeProofFormatService()],
          }),
        ],
      }),
      cache: new CacheModule({
        cache: new InMemoryLruCache({ limit: 100 }),
      }),
      w3cCredentials: new W3cCredentialsModule({}),
    },
  })

  // Register transports
  issuer.registerOutboundTransport(new WsOutboundTransport())
  issuer.registerOutboundTransport(new HttpOutboundTransport())
  issuer.registerInboundTransport(new HttpInboundTransport({ port: 3001 }))

  await issuer.initialize()
  return issuer
}
```
{% endcode %}

## Step 3: Set up the Holder Agent

The holder agent needs to resolve cheqd DIDs and handle JSON-LD credentials.

{% code title="Holder" %}
```typescript
const holderConfig: InitConfig = {
  label: 'cheqd-jsonld-holder',
  walletConfig: {
    id: 'cheqd-holder-wallet',
    key: 'testkey0000000000000000000000000',
  },
}

const initializeHolderAgent = async () => {
  const holder = new Agent({
    config: holderConfig,
    dependencies: agentDependencies,
    modules: {
      askar: new AskarModule({ ariesAskar }),
      dids: new DidsModule({
        resolvers: [new CheqdDidResolver()],
      }),
      credentials: new CredentialsModule({
        credentialProtocols: [
          new V2CredentialProtocol({
            credentialFormats: [new JsonLdCredentialFormatService()],
          }),
        ],
      }),
      proofs: new ProofsModule({
        proofProtocols: [
          new V2ProofProtocol({
            proofFormats: [new DifPresentationExchangeProofFormatService()],
          }),
        ],
      }),
      cache: new CacheModule({
        cache: new InMemoryLruCache({ limit: 100 }),
      }),
      w3cCredentials: new W3cCredentialsModule({}),
    },
  })

  // Register transports
  holder.registerOutboundTransport(new WsOutboundTransport())
  holder.registerOutboundTransport(new HttpOutboundTransport())
  holder.registerInboundTransport(new HttpInboundTransport({ port: 3002 }))

  await holder.initialize()
  return holder
}
```
{% endcode %}

## Step 4: Create Issuer DID

{% code title="Issuer" %}
```typescript
// Create a cheqd DID with Ed25519 verification method
const did = `did:cheqd:testnet:${utils.uuid()}`
const ed25519Key = await issuer.wallet.createKey({ keyType: KeyType.Ed25519 })

const createResult = await issuer.dids.create({
  method: 'cheqd',
  didDocument: new DidDocumentBuilder(did)
    .addController(did)
    .addVerificationMethod(
      getEd25519VerificationKey2018({
        key: ed25519Key,
        controller: did,
        id: `${did}#${ed25519Key.fingerprint}`,
      })
    )
    .addAssertionMethod(`${did}#${ed25519Key.fingerprint}`)
    .addAuthentication(`${did}#${ed25519Key.fingerprint}`)
    .build(),
})

if (!createResult.didState.did) {
  throw new Error('cheqd DID creation failed')
}

issuerDid = createResult.didState.did
console.log('Issuer DID created:', issuerDid)
```
{% endcode %}

## Step 5: Create connection between Issuer and Holder

Use any supported method to create a connection with the Holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended.

### 5a: Issuer creates Connection Invite

The Issuer agent will create a new connection invite for the Holder. This is needed to securely communicate between the Issuer and the Holder agents.

{% code title="Issuer" %}
```typescript
const createNewInvitation = async (agent: Agent) => {
  const outOfBandRecord = await agent.modules.oob.createInvitation()

  return {
    invitationUrl: outOfBandRecord.outOfBandInvitation.toUrl({ domain: 'http://localhost:3001' }),
    outOfBandRecord,
  }
}
```
{% endcode %}

### 5b: Holder receives invitation <a href="#id-1b-holder-receives-invitation" id="id-1b-holder-receives-invitation"></a>

The above request will have an `invitation` in the response. Holder will have to copy that invitation and pass URL as `invitationUrl` in the following code:

{% code title="Holder" %}
```typescript
const receiveInvitation = async (agent: Agent, invitationUrl: string) => {
  const { outOfBandRecord } = await agent.modules.oob.receiveInvitationFromUrl(invitationUrl)
  if (!outOfBandRecord) {
      throw new Error(redText(Output.NoConnectionRecordFromOutOfBand))
  }
  return outOfBandRecord
}
```
{% endcode %}

## Step 6: Set up Credential Event Listeners

Both agents need event listeners to handle the credential exchange protocol automatically.

{% code title="Issuer and Holder" %}
```typescript
import { CredentialEventTypes, CredentialState } from '@credo-ts/core'

const setupCredentialListener = (agent: Agent) => {
  agent.events.on(CredentialEventTypes.CredentialStateChanged, async ({ payload }) => {
    const { credentialRecord } = payload
    
    switch (credentialRecord.state) {
      case CredentialState.ProposalReceived:
        console.log('Issuer: Credential proposal received')
        await agent.credentials.acceptProposal({
          credentialRecordId: credentialRecord.id,
          comment: 'JSON-LD Credential Offer',
        })
        break
        
      case CredentialState.OfferReceived:
        console.log('Holder: Credential offer received, accepting...')
        await agent.credentials.acceptOffer({
          credentialRecordId: credentialRecord.id,
          credentialFormats: { jsonld: {} },
        })
        break
        
      case CredentialState.RequestReceived:
        console.log('Issuer: Credential request received, issuing credential...')
        await agent.credentials.acceptRequest({
          credentialRecordId: credentialRecord.id,
          comment: 'JSON-LD Credential',
        })
        break
        
      case CredentialState.CredentialReceived:
        console.log('Holder: Credential received, accepting...')
        await agent.credentials.acceptCredential({
          credentialRecordId: credentialRecord.id,
        })
        break
        
      case CredentialState.Done:
        console.log('Done: Credential exchange completed!')
        break
    }
  })
}
```
{% endcode %}

## Step 7: Holder proposes the Credential

In this example, we will initiate the credential issuance process by having the holder propose a credential.

{% code title="Holder" %}
```typescript
// Define the credential to be proposed
const credentialOptions = {
  credential: {
    '@context': [
      'https://www.w3.org/2018/credentials/v1',
      'https://w3id.org/citizenship/v1',
      'https://w3id.org/security/bbs/v1',
    ],
    id: 'https://cheqd.io/credentials/permanent-resident-card',
    type: ['VerifiableCredential', 'PermanentResidentCard'],
    issuer: issuerDid,
    issuanceDate: new Date().toISOString(),
    expirationDate: new Date(Date.now() + 10 * 365 * 24 * 60 * 60 * 1000).toISOString(), // 10 years
    credentialSubject: {
      id: 'did:example:holder123',
      type: ['PermanentResident', 'Person'],
      givenName: 'John',
      familyName: 'Doe',
      gender: 'Male',
      birthDate: '1990-01-01',
      birthCountry: 'United States',
      residentSince: '2020-01-01',
      lprNumber: '123-456-789',
      lprCategory: 'IR1',
    },
  },
  options: {
    proofType: 'Ed25519Signature2018',
    proofPurpose: 'assertionMethod',
  },
}
// Get Connection
const holderConnection = await receiveInvitation(holderAgent, invitationUrl)
// Holder proposes credential
console.log('Starting credential proposal...')
const credentialExchange = await holderAgent.credentials.proposeCredential({
  connectionId: holderConnection.id,
  protocolVersion: 'v2',
  credentialFormats: { jsonld: credentialOptions },
  comment: 'Requesting Permanent Resident Card',
})

console.log('Credential exchange initiated:', credentialExchange.id)

// The rest of the flow is handled automatically by event listeners
// Wait for completion...
```
{% endcode %}

The Credential Acceptance and storage is handled automatically by the event listeners registered for both Issuer and Holder.
