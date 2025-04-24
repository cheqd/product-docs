---
description: >-
  Create an AnonCreds Schema as a DID-Linked Resource on cheqd for issuing
  AnonCreds.
---

# Create AnonCreds Schema

To issue Verifiable Credentials, the issuer will have to first create a Schema and then a Credential Definition.

> ⚠️ **Before you begin...**
>
> Make sure you've correctly configured [the Credo agent with cheqd](../set-up-agent.md).

## Import a DID (optional)

In order to register a schema and credential definition, a DID must be added to the agent first. This can be done by calling `agent.dids.create()`, but this does require an endorser DID to be present as the `submitterDid`. This is optional, if you have created a DID from the agent before as described [here](../dids/create-did.md).

{% code title="Issuer" %}
```typescript
const seed = TypedArrayEncoder.fromString(`<seed>`) // Secret seed. Should be kept secure in production!
const cheqdDid = 'did:cheqd:testnet:d37eba59-513d-42d3-8f9f-d1df0548b675' // Cheqd DID to be imported

await agent.dids.import({
  did: cheqdDid,
  overwrite: true,
  privateKeys: [
    {
      privateKey: seed,
      keyType: KeyType.Ed25519,
    },
  ],
})
```
{% endcode %}

## Register a Schema

When you have a registered (or imported) a DID on the network and in your wallet, you can register a schema. Registering a schema requires four fields: `issuerId`, `name`, `version` and `attrNames`. It is important to note that the `issuerId` must be the same as a DID in your wallet.

{% code title="Issuer" %}
```typescript
const schemaResult = await agent.modules.anoncreds.registerSchema({
  schema: {
    attrNames: ['name', 'degree', 'date'],
    issuerId: '<did>',
    name: 'Example Schema to register',
    version: '1.0.0',
  },
  options: {},
})

if (schemaResult.schemaState.state === 'failed') {
  throw new Error(`Error creating schema: ${schemaResult.schemaState.reason}`)
}
```
{% endcode %}

### Parameters

<details>

<summary>schema (mandatory)</summary>

"attrNames": Array of attributes for the schema definition.

"issuerId": Issuer DID, use the did created by the Issuer.

"name": Name of the Schema

"version": Version of the Schema (To update existing Schema, use same name and different version)

</details>

<details>

<summary>options (optional)</summary>

Optional Key-Value pairs of additional options.

</details>







