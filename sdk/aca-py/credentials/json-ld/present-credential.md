---
description: >-
  Present a JSON-LD Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using ACA-Py.
---

# Present a Verifiable Credential

Using the `/present-proof-2.0` API endpoints, it is possible to present Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. This process enables secure and trustworthy sharing of verifiable credentials within the ACA-Py framework and cheqd ecosystem.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended. You can follow the same steps as described in [Issue a Verifiable Credential](issue-credential.md#step-1-create-a-connection-with-holder).

## Step 2: Send Proof Request

After connection is established, the Verifier can send a proof request to the Holder.

{% openapi-operation spec="aca-py" path="/present-proof-2.0/send-request" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

#### Sample Request Body

The request body will depend on the credential; for our citizenship credential, this is a sample.

```json
{
    "auto_verify": false,
    "connection_id": "{{issuer-conn-id}}",
    "comment": "Proof request for json-ld credential.",
    "presentation_request": {
        "dif": {
            "options": {
                "challenge": "{{$randomUUID}}",
                "domain": "test-citizenship"
            },
            "presentation_definition": {
                "id": "{{$randomUUID}}",
                "format": {
                    "ldp_vp": {
                        "proof_type": [
                            "Ed25519Signature2020"
                        ]
                    }
                },
                "input_descriptors": [
                    {
                        "id": "citizenship_input_1",
                        "name": "EU Driver's License",
                        "schema": [
                            {
                                "uri": "https://www.w3.org/2018/credentials#VerifiableCredential"
                            },
                            {
                                "uri": "https://w3id.org/citizenship#PermanentResident"
                            }
                        ],
                        "constraints": {
                            "fields": [
                                {
                                    "path": [
                                        "$.credentialSubject.familyName"
                                    ],
                                    "purpose": "The claim must be from one of the specified issuers",
                                    "filter": {
                                        "const": "SMITH"
                                    }
                                },
                                {
                                    "path": [
                                        "$.credentialSubject.givenName"
                                    ],
                                    "filter": {
                                        "const": "ALICE"
                                    },
                                    "purpose": "The claim must be from one of the specified issuers"
                                }
                            ]
                        }
                    }
                ]
            }
        }
    }
}
```

## Step 3: Holder sends Presentation Proof

Holder can get the stored credentials from own wallet and format a proof to send to the Verifier.

{% openapi-operation spec="aca-py" path="/present-proof-2.0/records/{pres_ex_id}/send-presentation" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

**Request Body**

For our citizenship credential example, the following is a sample request body.

```postman_json
{
    "dif": {
        "issuer_id": "{{issuer-did}}",
        "presentation_definition": {
            "id": "6d55f3ac-1dae-4306-a4bf-cf4fb93138b7",
            "format": {
                "ldp_vp": {
                    "proof_type": [
                        "Ed25519Signature2020"
                    ]
                }
            },
            "input_descriptors": [
                {
                    "id": "citizenship_input_1",
                    "name": "EU Driver's License",
                    "schema": [
                        {
                            "uri": "https://www.w3.org/2018/credentials#VerifiableCredential"
                        },
                        {
                            "uri": "https://w3id.org/citizenship#PermanentResident"
                        }
                    ],
                    "constraints": {
                        "fields": [
                            {
                                "path": [
                                    "$.credentialSubject.familyName"
                                ],
                                "purpose": "The claim must be from one of the specified issuers",
                                "filter": {
                                    "const": "SMITH"
                                }
                            },
                            {
                                "path": [
                                    "$.credentialSubject.givenName"
                                ],
                                "filter": {
                                    "const": "ALICE"
                                },
                                "purpose": "The claim must be from one of the specified issuers"
                            }
                        ]
                    }
                }
            ]
        }
    }
}
```

## Step 4: Verifier verifies Presentation

Verifier receives the presentation via the connection, and can use the following API to verify. The response must have `verified: true` .

{% openapi-operation spec="aca-py" path="/present-proof-2.0/records/{pres_ex_id}/verify-presentation" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

