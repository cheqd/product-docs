---
description: >-
  Issue a JSON-LD Verifiable Credential, signed by a did:cheqd Decentralized
  Identifier (DID), using the ACA-Py API endpoints.
---

# Issue a Verifiable Credential

Using the `/issue-credential-2.0` API endpoints, it is possible to issue Verifiable Credentials, signed by a cheqd DID, in a few clicks or lines of code. By following the following steps, you can effectively issue verifiable credentials using ACA-Py integrated with the cheqd ecosystem.

## Pre-requisite

For JSON-LD Credential Issuance, the Issuer DID must have `assertionMethod`  set to the `verificationMethod.id` . If your DID does not have `assertionMethod`, execute `POST /did/cheqd/update` API call to add new assertion method matching the `verificationMethod.id`.

## Step 1: Create a Connection with Holder

Use any supported method to create a connection with the Holder of the credential. Automated [out-of-band protocol](https://identity.foundation/didcomm-messaging/spec/#out-of-band-messages) is recommended.

### 1a: Issuer Creates Connection Invite

The Issuer agent will create a new connection invite for the Holder. This is needed to securely communicate between the Issuer and the Holder agents.

{% openapi-operation spec="aca-py" path="/out-of-band/create-invitation" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

#### Parameters and Request Body

For automated acceptance, pass the following parameters:

<details>

<summary>auto_accept</summary>

Set this to true

</details>

A simple request body is below:

```json
{
    "alias": "Holder",
    "handshake_protocols": ["https://didcomm.org/didexchange/1.0"],
    "use_public_did": false,
    "my_label": "Invitation to Holder"
}
```

### 1b: Holder receives invitation

The above request will have an `invitation` in the response. Holder will have to copy that invitation and pass in the body of the following API call.

{% openapi-operation spec="aca-py" path="/out-of-band/receive-invitation" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

#### Parameters and Request Body

<details>

<summary>auto_accept</summary>

Set this to true

</details>

<details>

<summary>alias</summary>

Pass an alias for the connection

</details>

<details>

<summary>use_existing_connection</summary>

Set this to true

</details>

The request body should be the `invitation` value from the `/create-invitation` call by the Issuer.

## Step 2: Holder creates local DID

For JSON-LD Credentials, the verification needs a Holder did as well. The Holder DID can be created locally by using the following API Call.

{% openapi-operation spec="aca-py" path="/wallet/did/create" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

**Request Body**

You can use the following request body to create the local did. Ensure the `key_type` matches the Issuer DID Key Type.

```json
{
  "method": "key",
  "options": {
    "key_type": "ed25519"
  }
}
```

## Step 3: Request Credential from Issuer

For this example, the Credential is requested from the Holder to the Issuer using the `/issue-credential-2.0/send-request` API. This request is sent to the Issuer, informing them about the credential request and its attributes in a JSON format.

{% openapi-operation spec="aca-py" path="/issue-credential-2.0/send-request" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

**Request Body**

The following request is a sample for creating a Citizenship credential.

```json
{
    "auto_remove": true,
    "comment": "Holder asking for Citizenship credential.",
    "connection_id": "{{holder-conn-id}}",
    "filter": {
        "ld_proof": {
            "credential": {
                "@context": [
                    "https://www.w3.org/2018/credentials/v1",
                    "https://w3id.org/citizenship/v1"
                ],
                "type": [
                    "VerifiableCredential",
                    "PermanentResident"
                ],
                "identifier": "{{$randomAlphaNumeric}}",
                "issuer": "{{issuer-did}}",
                "issuanceDate": "2020-01-01T12:00:00Z",
                "credentialSubject": {
                    "type": [
                        "PermanentResident",
                        "Person"
                    ],
                    "givenName": "ALICE",
                    "familyName": "SMITH",
                    "gender": "Female",
                    "birthCountry": "Bahamas",
                    "birthDate": "1958-07-17"
                }
            },
            "options": {
                "proofType": "Ed25519Signature2020"
            }
        }
    },
    "holder_did": "{{holder-local-did}}",
  "trace": false
}
```

## Step 4: Issuer accepts and issues credential

After the Issuer received and validated the request, they can issue the credential using the `/issue-credential-2.0/records/<credential-exchange-id>/issue` API endpoint. The `credential-exchange-id` can be retrieved by calling `GET /issue-credential-2.0/records?state=request-received` API.

{% openapi-operation spec="aca-py" path="/issue-credential-2.0/records/{cred_ex_id}/issue" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

## Step 5: Holder stores Credential

The Holder has to store the issued credential into their wallet using the following API. The `cred_ex_id` can be retrieved by running API `GET /issue-credential-2.0/records?state=credential-received` on the Holder side.

{% openapi-operation spec="aca-py" path="/issue-credential-2.0/records/{cred_ex_id}/store" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

## Step 6: Check the received Credential

As this is a JSON-LD Credential, Holder must use `POST /credentials/w3c` API to check the credential.

{% openapi-operation spec="aca-py" path="/credentials/w3c" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

**Request Body**

For our citizenship credential example, the following request can be used

```json
{
    "contexts": [
        "https://www.w3.org/2018/credentials/v1",
        "https://w3id.org/citizenship/v1"
    ],
    "proof_types": [
        "Ed25519Signature2020"
    ]
}
```
