# Issue a Verifiable Credential

Using [AnonCreds](https://anoncreds-wg.github.io/anoncreds-spec/) and the [Issue Credential V2 Protocol](https://github.com/hyperledger/aries-rfcs/blob/main/features/0453-issue-credential-v2/README.md), it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. By following the following steps, you can effectively issue verifiable credentials using Credo Agent integrated with the cheqd ecosystem.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended.

### 1a: Issuer Creates Connection Invite

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

### 1b: Holder receives invitation

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

## Step 2: Prepare  and Send Credential Offer

Generate a credential offer and send to the holder, informing them about the available credential and its attributes.

{% code title="Issuer" %}
```typescript
await this.agent.modules.credentials.offerCredential({
      connectionId: connectionRecord.id,
      protocolVersion: 'v2',
      credentialFormats: {
        anoncreds: {
          attributes: [
            {
              name: 'name',
              value: 'Alice Smith',
            },
            {
              name: 'degree',
              value: 'Computer Science',
            },
            {
              name: 'date',
              value: '01/01/2022',
            },
          ],
          credentialDefinitionId: credentialDefinition.credentialDefinitionId,
        },
      },
    })
```
{% endcode %}

## Step 3: Holder accepts credential offer

When we want to accept a credential, we have to listen to incoming credentials and handle accordingly. In this example we do not have any user interaction, but is likely that your application would have a user-interface which would display the credential. When receiving a credential offer you can get the values from `credentialExchangeRecord.credentialAttributes`.

{% code title="Holder" %}
```typescript
this.agent.events.on(CredentialEventTypes.CredentialStateChanged,
  async ({ payload }: CredentialStateChangedEvent) => {
  switch (payload.credentialRecord.state) {
    case CredentialState.OfferReceived:
      console.log('received a credential')
      // custom logic here
      await this.agent.modules.credentials.acceptOffer({ credentialRecordId: payload.credentialRecord.id })
      break
    case CredentialState.Done:
      console.log(`Credential for credential id ${payload.credentialRecord.id} is accepted`)
      // For demo purposes we exit the program here.
      process.exit(0)
  }
})
```
{% endcode %}

