---
description: >-
  Create an AnonCreds Schema as a DID-Linked Resource on cheqd for issuing
  AnonCreds.
---

# Create AnonCreds Schema

To issue Verifiable Credentials, the issuer will have to first create a Schema and then a Credential Definition.

## Create Schema

The request body must contain the `schema` object, which defines the attributes, name and version of the Schema. This will create a [DID-Linked Resource](https://docs.cheqd.io/product/sdk/veramo-plugin/did-linked-resources) of type `anonCredsSchema`.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/anoncreds/schema" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Request Body

<details>

<summary>options (optional)</summary>

Optional Key-Value pairs of additional options.

</details>

<details>

<summary>schema (mandatory)</summary>

"attrNames": Array of attributes for the schema definition.

"issuerId": Issuer DID, use the did created by the Issuer.

"name": Name of the Schema

"version": Version of the Schema (To update existing Schema, use same name and different version)

</details>

## Check the new Schema

{% openapi src="../../../.gitbook/assets/swagger.json" path="/anoncreds/schema/{schema_id}" method="get" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}







