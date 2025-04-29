---
description: >-
  Create an Credential Definition as a DID-Linked Resource on cheqd for issuing
  AnonCreds.
---

# Create AnonCreds Credential Definition

## Register a Credential Definition

After registering a schema, a credential definition can be registered based on the schema. The credential definition, amongst more things, binds the schema to a specific issuer. Schemas can be reused between issuers, but a credential definition is specific to an issuer. In a credential definition revocation can also be specified. This section will not go in-depth about revocation.

{% code title="Issuer" %}
```typescript
const credentialDefinitionResult = await agent.modules.anoncreds.registerCredentialDefinition({
  credentialDefinition: {
    tag: 'default',
    issuerId: '<did>',
    schemaId: schemaResult.schemaState.schemaId,
  },
  options: {
    supportRevocation: false,
  },
})

if (credentialDefinitionResult.credentialDefinitionState.state === 'failed') {
  throw new Error(
    `Error creating credential definition: ${credentialDefinitionResult.credentialDefinitionState.reason}`
  )
}
```
{% endcode %}

The request body must contain the `credential_definition` object with the Issuer DID and the Schema ID created in the previous steps.

To enable revocation, the `options` must contain the revocation flag, and the size of the revocation registry. Also, ensure that a [Tails Server](https://github.com/bcgov/indy-tails-server) is configured for the issuer.

### Parameters

<details>

<summary>credential_definition (mandatory)</summary>

"issuerId": DID of the Issuer.

"schemaId": The AnonCreds Schema ID created by the Issuer (must be of the same DID).

"tag": Additional Name to identify the Credential Definition.

</details>

<details>

<summary>options (optional)</summary>

"support\_revocation": Boolean value to indicate that the Credential can be revoked or not. (Tails server must be configured if this is set to \`true\`)

"revocation\_registry\_size": Maximum size of the Revocation Registry.

</details>







