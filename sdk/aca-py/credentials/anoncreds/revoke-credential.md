---
description: >-
  Revoke an AnonCreds Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using ACA-Py.
---

# Revoke a Verifiable Credential

Revoking credentials is crucial for maintaining control over issued credentials and preventing misuse in the cheqd ecosystem. To revoke credentials within the ACA-Py framework, follow these steps:

## Step 1: Issuer identifies Credential to be revoked

The Issuer must determine the credential that needs to be revoked by getting the list of issued credentials and the connection id.

## Step 2: Revoke Credential

Initiate the credential revocation process by supplying the necessary credential identifiers like the connection id and the credential exchange id.

{% openapi src="../../../../.gitbook/assets/swagger.json" path="/anoncreds/revocation/revoke" method="post" %}
[swagger.json](../../../../.gitbook/assets/swagger.json)
{% endopenapi %}

#### Sample Request Body

```json
{
    "comment": "Revoke issued credential",
    "connection_id": "<issuer--to-holder-conn-id>",
    "cred_ex_id": "<issuer-cred-exchange-id>",
    "notify": true,
    "publish": true
}
```

## Step 3: Verify Revocation

Follow the steps to [Present a Verifiable Credential](present-credential.md) to verify that the credential has been successfully revoked. The response should have `verified: false`.
