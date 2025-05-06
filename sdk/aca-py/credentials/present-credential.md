---
description: >-
  Present a Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using ACA-Py.
---

# Present a Verifiable Credential

Using the `/present-proof-2.0` API endpoints, it is possible to present Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. This process enables secure and trustworthy sharing of verifiable credentials within the ACA-Py framework and cheqd ecosystem.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended. You can follow the same steps as described in [Issue a Verifiable Credential](issue-credential.md#step-1-create-a-connection-with-holder).

## Step 2: Send Proof Request

After connection is established, the Verifier can send a proof request to the Holder.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/send-request" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

#### Sample Request Body

The request body will depend on the credential, but here is a sample.

```json
{
  "connection_id": "<verifier-to-holder-conn-id>",
  "auto_verify": false,
  "auto_remove": false,
  "comment": "Presentation request from Issuer",
  "presentation_request": {
    "anoncreds": {
        "name": "proof",
        "version": "1.0",
        "requested_predicates": {
            "additionalProp1": {
                "name": "score",
                "p_value": 40,
                "p_type": ">",
                "restrictions": [{"cred_def_id": "<cred-def-id>"}]
            }
        },
        "requested_attributes": {},
        "non_revoked": {"to": <some-time-in-future>}
    }
  },
  "trace": false
}
```

## Step 3: Holder sends Presentation Proof

Holder can get the stored credentials from own wallet and format a proof to send to the Verifier.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/records/{pres_ex_id}/send-presentation" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

## Step 4: Verifier verifies Presentation

Verifier receives the presentation via the connection, and can use the following API to verify. The response must have `verified: true` .

{% openapi src="../../../.gitbook/assets/swagger.json" path="/present-proof-2.0/records/{pres_ex_id}/verify-presentation" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

