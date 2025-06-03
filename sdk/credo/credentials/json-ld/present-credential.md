---
description: >-
  Present a JSON-LD Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using Credo.
---

# Present a Verifiable Credential

Verifiable Credentials signed by a `did:cheqd` can be securely presented using the **Dif proof format** and the **Present Proof Protocol v2** within the Credo framework. This enables trust-minimised, selective disclosure of credential data between a **Holder** and a **Verifier**.

## Prerequisites <a href="#prerequisites" id="prerequisites"></a>

Before presenting a credential:

* A **Verifiable Credential** must have been issued and accepted by the Holder
* A **Credo Agent** is running for both the Verifier and the Holder
* A **DIDComm connection** exists between Holder and Verifier (via OOB or another method)
* Both agents are configured with [Credo packages](issue-credential.md#step-2-set-up-the-issuer-agent) needed for JSON-LD Credential Proof.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended. You can follow the same steps as described in [Issue a Verifiable Credential](issue-credential.md#step-5-create-connection-between-issuer-and-holder).

## Step 2: Register Proof Event Listeners

Both agents need event listeners to handle the proof exchange protocol automatically.

{% code title="Verifier and Holder" %}
```typescript
import { ProofEventTypes, ProofState } from '@credo-ts/core'

const setupProofListener = (agent: Agent) => {
  agent.events.on(ProofEventTypes.ProofStateChanged, async ({ payload }) => {
    const { proofRecord } = payload
    
    switch (proofRecord.state) {
      case ProofState.RequestReceived:
        console.log('Holder: Proof request received, creating presentation...')
        const requestedCredentials = await agent.proofs.selectCredentialsForRequest({
          proofRecordId: proofRecord.id,
        })
        
        await agent.proofs.acceptRequest({
          proofRecordId: proofRecord.id,
          proofFormats: {
            presentationExchange: {
              credentials: requestedCredentials.proofFormats['presentation-exchange']?.credentials || {},
            },
          },
        })
        break
        
      case ProofState.PresentationReceived:
        console.log('Issuer: Presentation received, verifying...')
        await agent.proofs.acceptPresentation({
          proofRecordId: proofRecord.id,
        })
        break
        
      case ProofState.Done:
        console.log('Proof verification completed!')
        const proof = await agent.proofs.getById(proofRecord.id)
        console.log('Proof is valid:', proof.isVerified)
        break
    }
  })
}
```
{% endcode %}

Step 3: Send Proof Request

After the connection is established and event handlers registered, the Verifier can send a proof request to the Holder.

{% code title="Verifier/Issuer" %}
```typescript
// Define what we want to verify
const presentationDefinition = {
  id: 'permanent-resident-verification',
  input_descriptors: [
    {
      id: 'permanent-resident-card',
      name: 'Permanent Resident Card',
      purpose: 'Verify permanent resident status',
      constraints: {
        fields: [
          {
            path: ['$.type'],
            filter: {
              type: 'array',
              contains: { const: 'PermanentResidentCard' },
            },
          },
          {
            path: ['$.credentialSubject.givenName'],
            filter: { type: 'string' },
          },
          {
            path: ['$.credentialSubject.familyName'],
            filter: { type: 'string' },
          },
          {
            path: ['$.credentialSubject.lprNumber'],
            filter: { type: 'string' },
          },
        ],
      },
    },
  ],
}
// Get Connection
const {invitationUrl, issuerConnection} = await createInvitation(issuerAgent)

console.log('Requesting proof verification...')
const proofExchange = await issuerAgent.proofs.requestProof({
  connectionId: issuerConnection.id,
  protocolVersion: 'v2',
  proofFormats: {
    presentationExchange: { presentationDefinition },
  },
  comment: 'Please present your Permanent Resident Card for verification',
})
console.log('Proof request sent:', proofExchange.id)
// The rest of the flow is handled automatically by event listeners
```
{% endcode %}

The Proof Acceptance and Presentation is handled automatically by the event listeners registered for both Verifier and Holder.

## Next Steps <a href="#next-steps" id="next-steps"></a>

For more tutorials and examples, visit [Credo Docs](https://credo.js.org/guides).
