---
description: Present a Verifiable Credential using Credo
---

# Present a Verifiable Credential (AnonCreds)

Verifiable Credentials signed by a `did:cheqd` can be securely presented using the **AnonCreds proof format** and the **Present Proof Protocol v2** within the Credo framework. This enables trust-minimised, selective disclosure of credential data between a **Holder** and a **Verifier**.

## Prerequisites

Before presenting a credential:

* A **Verifiable Credential** must have been issued and accepted by the Holder
* A **Credo Agent** is running for both the Verifier and the Holder
* A **DIDComm connection** exists between Holder and Verifier (via OOB or another method)
* Both agents are configured with:
  * `@credo-ts/anoncreds`
  * `@credo-ts/didcomm`

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended. You can follow the same steps as described in [Issue a Verifiable Credential](issue-a-verifiable-credential.md#step-1-create-a-connection-with-holder).

## Step 2: Send Proof Request

After connection is established, the Verifier can send a proof request to the Holder.

{% code title="Verifier/Issuer" %}
```typescript
await this.agent.modules.proofs.requestProof({
  protocolVersion: 'v2',
  connectionId: connectionRecord.id,
  proofFormats: {
    anoncreds: {
      name: 'proof-request',
      version: '1.0',
      requested_attributes: {
        name: {
          name: 'name',
          restrictions: [
            {
              cred_def_id: this.credentialDefinition?.credentialDefinitionId,
            },
          ],
        },
      },
    },
  },
})
```
{% endcode %}

## Step 3: Holder sends Presentation Proof

Holder can get the stored credentials from own wallet and format a proof to send to the Verifier.

When we want to send a proof, we have to listen to incoming proof requests and handle accordingly. In this example we do not have any user interaction, but is likely that your application would have a user-interface which would display the request.

<pre class="language-typescript" data-title="Holder"><code class="lang-typescript">this.agent.events.on(ProofEventTypes.ProofStateChanged
  async ({ payload }: ProofStateChangedEvent) => {
<strong>    if (payload.proofRecord.state === ProofState.RequestReceived) {
</strong><strong>      const requestedCredentials = await this.agent.modules.proofs.selectCredentialsForRequest({
</strong>        proofRecordId: payload.proofRecord.id,
      })
      await this.agent.modules.proofs.acceptRequest({
        proofRecordId: proofRecord.id,
        proofFormats: requestedCredentials.proofFormats,
      })
<strong>    }
</strong>})
</code></pre>

### What’s Happening Behind the Scenes?

* The Verifier sends a proof request with specific attributes and credential requirements
* The Holder uses locally stored credentials to generate a **selective disclosure proof**
* The proof is signed using AnonCreds and returned to the Verifier over DIDComm
* The Verifier cryptographically validates the proof and the issuing DID (`did:cheqd`)

## Next Steps

For more tutorials and examples, visit [Credo Docs](https://credo.js.org/guides).

