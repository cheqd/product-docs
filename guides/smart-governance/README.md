# Governance File

## Overview

For Self-Sovereign Identity ecosystems to work in practice, governance is a key component for defining rules, policies and processes under which the ecosystem participants need to comply.

On cheqd, Governance files can be tied to DIDs and identified with their own DID URLs, in order to act as a trusted hub in a particular ecosystem for:

1. Jurisdictions and geographies authorised;
2. Schemas used for Verifiable Credentials in the ecosystem;
3. Trusted issuer list;
4. Revocation list;
5. Roles defined for the ecosystem;
6. Permissions;
7. Actions, specifying how certain credentials may be obtained or verified;
8. Privileges, delegating which roles can carry out certain actions;
9. Workflows, guiding adopters through the ecosystem's processes.

Through this model, ecosystem authorities will be able create a set of governance actions which can be:

1. Verifiable using DID URL resolution;
2. Updated only be authorised parties who hold specific cryptographic keys
3. Public and transparent
4. Baked into SDKs to enable ecosystem authorities to create Governance files easily

## Identifying and storing Governance files

Governance Files will be stored on the cheqd network and will be tied to a parent DID and DID Document

Each Governance File version will be identifiable via a unique DID URL.

For example, the following DID is shown with a Governance File attached

```json
{
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1",
      "https://w3id.org/security/suites/ed25519-2020/v1"
    ],
    "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75",
    "verificationMethod": [
      {
        "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75#authKey1",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75",
        "publicKeyMultibase": "z6MkuGoZyDyji4sApi9L79CG484hoQPmtuqJSTbUpdrVQAqB"
      },
    ],
    "authentication": "did:cheqd:testenet28b25f5d-36d6-45e0-9366-ec67db4aac75#authKey1",
    "service": [
      {
        "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75#GovernanceFile",
        "type": "linkedResource",
        "serviceEndpoint" [
            "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/569b740e-6bf2-41a7-b9ba-acba2ae5e8ad"
        ]
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2022-09-16T11:15:20Z",
    "versionId": "ea2b76cf-a118-403a-8f49-244e56c9dcb8",
    "linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/569b740e-6bf2-41a7-b9ba-acba2ae5e8ad",
        "resourceCollectionId": "28b25f5d-36d6-45e0-9366-ec67db4aac75",
        "resourceId": "569b740e-6bf2-41a7-b9ba-acba2ae5e8ad",
        "resourceName": "Governance File",
        "resourceType": "governanceFile",
        "mediaType": "application/json",
        "created": "2022-09-16T11:15:52Z",
        "checksum": "0f503dfbff29de9ac74fd07f1720ba560eb388e28062367884890c311736ec9d",
        "previousVersionId": "",
        "nextVersionId": ""
      }
    ]
  }
}
```

Therefore, the Governance File is referenced in the DID Document Metadata and may be optionally referenced in the `service` section of the DID Document itself.

## Creating a Governance File

Below is an example of a Governance File that is being used to govern an ecosystem of museums issuing museum pass Verifiable Credentials.&#x20;

```json
{
    "@context": "https://docs.cheqd.io/identity",
    "name": "Museum Pass Ecosystem",
    "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/569b740e-6bf2-41a7-b9ba-acba2ae5e8ad",
    "desctiption": "This file lists the core and primary governance functions of a Museum Pass Ecosystem",
    "jurisdictions": [
        "GATF City"
    ],
    "geos": [
        "GATF Land",    
    ],
    "schemas": [
        {
            "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/9a299740-320d-4f7b-83f2-027ef5bdb654",
            "name": "Museum Pass: Standard",
        },
        {
            "id": "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/2e5ac126-7f9b-42ff-b29b-619011e371c2",
            "name": "Museum Pass: Premium"
        }
    ],
    "trustedIssuerList": [
        {
            "id": "did:cheqd:testnet:b68c46ab-b821-4b03-aecb-6ce6c659de83",
            "name": "Museum trusted issuer list",
        }
    ],
    "revocationList": null,
    "roles": [
        "holder",
        "museum_pass_issuer",
        "museum_pass_verifier",
    ],
    "permissions": [
        {
            "grant": 
        },
    ],
    "actions": [
        {
            "name": "issue_standard_museum_pass",
            "protocol": "https://didcomm.org/connections/1.0",
            "startMessage": "offer-credential",
            "details": {
                "schema" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/9a299740-320d-4f7b-83f2-027ef5bdb654",
                "presentationDefinition" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/d30c2160-60fd-4db3-80b2-507926357d62",
            }
        },
        {
            "name": "issue_premium_museum_pass",
            "protocol": "https://didcomm.org/connections/1.0",
            "startMessage": "offer-credential",
            "details": {
                "schema" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/2e5ac126-7f9b-42ff-b29b-619011e371c2",
                "presentationDefinition" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/2718aa7b-8134-4d26-99ca-21e909ee51fa",
            }
        },
        {
            "name": "verify_standard_museum_pass",
            "protocol": "https://didcomm.org/present-proof/1.0",
            "startMessage": "request-presentation",
            "details": {
                "schema" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/9a299740-320d-4f7b-83f2-027ef5bdb654",
                "presentationDefinition" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/d30c2160-60fd-4db3-80b2-507926357d62",
            }
        },            
        {
            "name": "verify_premium_museum_pass",
            "protocol": "https://didcomm.org/present-proof/1.0",
            "startMessage": "request-presentation",
            "details": {
                "schema" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/2e5ac126-7f9b-42ff-b29b-619011e371c2",
                "presentationDefinition" "did:cheqd:testnet:28b25f5d-36d6-45e0-9366-ec67db4aac75/resources/2718aa7b-8134-4d26-99ca-21e909ee51fa",
            }
        }
    ],
    "privelages": [
        {
            "grant": [
                "issue_standard_museum_pass"
            ],
            "when": {
                "any": [
                    {
                        "role": "museum_pass_issuer"
                    }
                ]
            }
        },
        {
            "grant": [
                "issue_premium_museum_pass"
            ],
            "when": {
                "any": [
                    {
                        "role": "museum_pass_issuer"
                    }
                ]
            }
        },
    ],
    "flows": null
}
    
        
    

```
