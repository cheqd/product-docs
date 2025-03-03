# Present a Verifiable Credential

It is possible to present Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. This process enables secure and trustworthy sharing of verifiable credentials within the Credo framework and cheqd ecosystem.

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

## Next Steps

For more tutorials and examples, visit [Credo Docs](https://credo.js.org/guides).

