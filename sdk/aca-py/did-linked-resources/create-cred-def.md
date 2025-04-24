---
description: >-
  Create an Credential Definition as a DID-Linked Resource on cheqd for issuing
  AnonCreds.
---

# Create AnonCreds Credential Definition

## Create Credential Definition

The request body must contain the `credential_definition` object with the Issuer DID and the Schema ID created in the previous steps.

To enable revocation, the `options` must contain the revocation flag, and the size of the revocation registry. Also, ensure that a [Tails Server](https://github.com/bcgov/indy-tails-server) is configured for the issuer.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/anoncreds/credential-definition" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Request Body

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

## Check the new Credential Definition

{% openapi src="../../../.gitbook/assets/swagger.json" path="/anoncreds/credential-definition/{cred_def_id}" method="get" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}







