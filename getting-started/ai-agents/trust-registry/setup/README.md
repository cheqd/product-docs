---
description: Walkthrough for setting up Trust Registries for your AI Agents.
---

# Setup AI Agent Trust Registry

Users are able to build AI Agent Trust Registries using our cheqd Studio APIs. The following steps will enable organisations, governance authorities, auditors and AI Agents to establish permissions, rules and hierarchy between each other.

## Step 1: Set up your cheqd Studio account

Make sure you have set up your account with **cheqd Studio** and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with <strong>cheqd Studio</strong> and log in to start using the APIs.</td><td><a href="../../../studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a Root DID

The first step for any trust registry is a Root DID, which acts as a trust anchor for the chain of trust below. This DID should be for the highest level of trust in your ecosystem, such as a governance authority, a managing company or an auditor.&#x20;

{% hint style="success" %}
For more basic trust registries, the company issuing the AI Agent credentials may also be the Root of Trust with the Root DID.&#x20;
{% endhint %}

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../../../../studio/dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Design Schemas for your Ecosystem

When you accredit an AI Agent or Organisation that builds AI Agents, you need to do so for a particular purpose. These purposes are defined in schemas, containing the fields that MUST or MAY be present in an accreditation or credential.&#x20;

We have created some template schemas that we suggest you use within your trust registry!

#### 3.1 Verifiable Attestation

This is a schema for a Verifiable Credential issued to your AI Agent from a Trusted Issuer. The Issuer attests to features of the AI Agent, hence we call it a Verifiable Attestation.

<details>

<summary>Verifiable Attestation Schema</summary>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Verifiable Attestation",
  "description": "The schema defines a generic structure for any Verifiable Credentials according to the VCDM v1.1",
  "type": "object",
  "properties": {
    "@context": {
      "description": "Semantic context for the issued credential. First element MUST be https://www.w3.org/2018/credentials/v1",
      "type": "array",
      "items": {
        "type": "string",
        "format": "uri"
      },
      "contains": {
        "const": "https://www.w3.org/2018/credentials/v1"
      },
      "minItems": 1,
      "uniqueItems": true
    },
    "id": {
      "description": "Globally unique identifier for the issued credential",
      "type": "string",
      "format": "uri"
    },
    "type": {
      "description": "Full type chain, used to identify the credential base types",
      "type": "array",
      "items": {
        "type": "string"
      },
      "contains": {
        "type": "string",
        "const": "VerifiableAttestation"
      },
      "uniqueItems": true
    },
    "issuer": {
      "description": "Defines a property for expressing the issuer of a Verifiable Credential",
      "oneOf": [
        {
          "description": "DID of the credential issuer",
          "type": "string",
          "format": "uri"
        },
        {
          "type": "object",
          "required": ["id"],
          "properties": {
            "id": {
              "description": "DID of the credential issuer",
              "type": "string",
              "format": "uri"
            }
          }
        }
      ]
    },
    "issuanceDate": {
      "description": "Defines the date and time, when the issued credential becomes valid",
      "type": "string",
      "format": "date-time"
    },
    "issued": {
      "description": "Defines when the issued credential was issued",
      "type": "string",
      "format": "date-time"
    },
    "validFrom": {
      "description": "Defines the date and time, when the issued credential becomes valid",
      "type": "string",
      "format": "date-time"
    },
    "validUntil": {
      "description": "Defines the date and time, when the issued credential expires",
      "type": "string",
      "format": "date-time"
    },
    "expirationDate": {
      "description": "Defines the date and time, when the issued credential expires",
      "type": "string",
      "format": "date-time"
    },
    "credentialSubject": {
      "description": "Defines information about the subject that is defined by the type chain",
      "anyOf": [
        {
          "$ref": "#/$defs/credentialSubject"
        },
        {
          "type": "array",
          "items": {
            "$ref": "#/$defs/credentialSubject"
          }
        }
      ]
    },
    "credentialStatus": {
      "description": "Defines suspension and/or revocation details for the issued credential. Further redefined by the type extension",
      "anyOf": [
        {
          "$ref": "#/$defs/credentialStatus"
        },
        {
          "type": "array",
          "items": {
            "$ref": "#/$defs/credentialStatus"
          }
        }
      ]
    },
    "credentialSchema": {
      "description": "One or more schemas that validate the Verifiable Credential.",
      "anyOf": [
        {
          "$ref": "#/$defs/credentialSchema"
        },
        {
          "type": "array",
          "items": {
            "$ref": "#/$defs/credentialSchema"
          }
        }
      ]
    },
    "termsOfUse": {
      "description": "Contains the terms under which the issued credential was issued",
      "anyOf": [
        {
          "$ref": "#/$defs/termsOfUse"
        },
        {
          "type": "array",
          "items": {
            "$ref": "#/$defs/termsOfUse"
          }
        }
      ]
    },
    "evidence": {
      "description": "Contains the optional evidence used to issue this credential",
      "anyOf": [
        {
          "$ref": "#/$defs/evidence"
        },
        {
          "type": "array",
          "items": {
            "$ref": "#/$defs/evidence"
          }
        }
      ]
    }
  },
  "required": [
    "@context",
    "id",
    "type",
    "issuer",
    "credentialSubject"
  ],
  "$defs": {
    "credentialSubject": {
      "description": "Defines information about the subject that is defined by the type chain",
      "type": "object",
      "properties": {
        "id": {
          "description": "Defines the DID of the subject that is described by the issued credential",
          "type": "string",
          "format": "uri"
        }
      }
    },
    "credentialStatus": {
      "description": "Defines suspension and/or revocation details for the issued credential. Further redefined by the type extension",
      "type": "object",
      "properties": {
        "id": {
          "description": "Exact identity for the credential status",
          "type": "string",
          "format": "uri"
        },
        "type": {
          "description": "Defines the revocation type extension",
          "type": "string"
        }
      },
      "required": ["id", "type"]
    },
    "credentialSchema": {
      "description": "Contains information about the credential schema on which the issued credential is based",
      "type": "object",
      "properties": {
        "id": {
          "description": "References the credential schema stored on the Trusted Schemas Registry (TSR) on which the Verifiable Authorisation is based on",
          "type": "string",
          "format": "uri"
        },
        "type": {
          "description": "Defines credential schema type",
          "type": "string"
        }
      },
      "required": ["id", "type"]
    },
    "termsOfUse": {
      "description": "Contains the terms under which the issued credential was issued",
      "type": "object",
      "properties": {
        "id": {
          "description": "Contains a URL that points to where more information about this instance of terms of use can be found.",
          "type": "string"
        },
        "type": {
          "description": "Defines the type extension",
          "type": "string"
        }
      },
      "required": ["type"]
    },
    "evidence": {
      "type": "object",
      "properties": {
        "id": {
          "description": "If present, it SHOULD contain a URL that points to where more information about this instance of evidence can be found.",
          "type": "string"
        },
        "type": {
          "anyOf": [
            {
              "description": "Defines the evidence type extension",
              "type": "string"
            },
            {
              "description": "Defines the evidence type extension",
              "type": "array",
              "items": {
                "type": "string"
              }
            }
          ]
        }
      },
      "required": ["type"]
    }
  }
}
```



</details>

This schema can be always retrieved from the cheqd ledger at:

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020" %}

#### 3.2 Verifiable Accreditation

This is a schema for a Verifiable Credential between two DIDs, to accredit the DIDs for specific purposes. The `accreditedFor` section of the Accreditation can be modified with specific schemas for AI Agents.

<details>

<summary>Verifiable Accreditation Schema</summary>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Verifiable Accreditation",
  "description": "Schema of a Verifiable Accreditation",
  "type": "object",
  "allOf": [
    {
      "$ref": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020"
    },
    {
      "properties": {
        "credentialSubject": {
          "description": "Defines additional information about the subject that is described by the Verifiable Accreditation",
          "type": "object",
          "properties": {
            "id": {
              "description": "Defines a unique identifier of the Verifiable Accreditation",
              "type": "string",
              "format": "uri"
            },
            "accreditedFor": {
              "description": "Defines a list of claims that define/determine the authorisation of an Issuer to issue certain types of VCs",
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "schemaId": {
                    "description": "Schema, registered in Trusted  Schemas Registry, which the accredited organisation is allowed to issue, as per their accreditation",
                    "type": "string",
                    "format": "uri"
                  },
                  "types": {
                    "type": "array",
                    "items": {
                      "type": "string"
                    }
                  },
                  "limitJurisdiction": {
                    "anyOf": [
                      {
                        "description": "Defines the jurisdiction for which the accreditation is valid",
                        "type": "string",
                        "format": "uri"
                      },
                      {
                        "type": "array",
                        "description": "Defines the jurisdictions for which the accreditation is valid",
                        "items": {
                          "type": "string",
                          "format": "uri"
                        }
                      }
                    ]
                  }
                },
                "required": ["schemaId", "types"]
              }
            }
          },
          "required": ["id", "accreditedFor"]
        },
        "credentialStatus": {
          "description": "Defines revocation details for the issued credential. Further redefined by type extension",
          "type": "object",
          "properties": {
            "id": {
              "description": "Exact identity for the credential status",
              "type": "string",
              "format": "uri"
            },
            "type": {
              "description": "Defines the revocation status type",
              "type": "string",
              "const": "EbsiAccreditationEntry"
            }
          },
          "required": ["id", "type"]
        }
      },
      "required": [
        "credentialSubject",
        "termsOfUse"
      ]
    }
  ]
}

```



</details>

This schema can be always retrieved from the cheqd ledger at:

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020" %}

{% hint style="success" %}
We suggest that you use the same schemas that we have already made for Verifiable Accreditations and Attestations, although this is not a requirement
{% endhint %}

#### 3.3 Custom Schemas

Builders can create custom schemas for their AI Agents, or for the accreditations between different organisations in their ecosystems. This is achieved through editing the `accreditedFor` section of a Verifiable Attestation above.&#x20;

For example, the following schema shows how the configuration of an AI Agent can be represented within a schema:

<details>

<summary>AI Agent Authorization Schema</summary>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "allOf": [
    {
      "$ref": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020"
    },
    {
    "properties": {
      "credentialSubject": {
        "type": "object",
        "properties": {
          "id": {
            "type": "string",
            "pattern": "uri"
          },
          "aiAgentName": {
            "type": "string"
          },
          "aiAgentVersion": {
            "type": "string"
          },
          "model": {
            "type": "string"
          },
          "modelVersion": {
            "type": "string"
          },
          "contextWindow": {
            "type": "integer",
            "minimum": 0
          },
          "temperature": {
            "type": "number",
            "minimum": 0,
            "maximum": 1
          },
          "topK": {
            "type": "integer",
            "minimum": 0
          },
          "topP": {
            "type": "number",
            "minimum": 0,
            "maximum": 1
          },
          "maxTokens": {
            "type": "integer",
            "minimum": 0
          },
          "fineTuned": {
            "type": "boolean"
          },
          "fineTuningDetails": {
            "type": "string",
            "nullable": true
          },
          "safetyRating": {
            "type": "string"
          },
          "evaluationMetrics": {
            "type": "array",
            "items": {
              "type": "string"
            }
          },
          "certificationAuthority": {
            "type": "string"
          },
          "validUntil": {
            "type": "string",
            "format": "date-time"
          }
        },
        "required": [
          "id",
          "aiAgentName",
          "model"
        ]
      },
      "proof": {
        "description": "Contains information about the proof",
        "type": "object",
        "properties": {
          "type": {
            "description": "Defines the proof type",
            "type": "string"
          },
          "proofPurpose": {
            "description": "Defines the purpose of the proof",
            "type": "string"
          },
          "created": {
            "description": "Defines the date and time, when the proof has been created",
            "type": "string",
            "format": "date-time"
          },
          "verificationMethod": {
            "description": "Contains information about the verification method / proof mechanisms",
            "type": "string"
          },
          "jws": {
            "description": "Defines the proof value in JWS format",
            "type": "string"
          }
        },
        "required": [
          "type",
          "proofPurpose",
          "created",
          "verificationMethod",
          "jws"
        ]
      }
    },
    "required": ["credentialSubject", "proof"]
    }
  ]
}
```



</details>

## Step 4: Publish your schemas to cheqd as DID-Linked Resources

With the Root DID you created in Step 2, you can create links to your schemas, storing them on-chain in a resolvable format.

You can follow the tutorial here to publish your schemas as DID-Linked Resources. Generally we use the `resourceType` of `JSONSchemaValidator2020` for JSON schemas written to the ledger.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Link resources such as schemas to your DID, publishing as a DID-Linked Resource.</td><td><a href="../../../../studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>

This will store the schemas securely on the cheqd Network, where they can be fetched using DID URLs.

## Step 5: Issue a Root Authorization for the Trust Chain

The Root Authorization in trust registries on cheqd is called a `rootAuthorizationForTrustChain`. This authorization contains information about the governance framework the AI Agents will operate in, and signifies to trust registry resolvers that they have reached the intended Root.

Authorizations are issued between two DIDs (which may be the same). As such, if you are managing the entire ecosystem, you may need to create multiple DIDs for different roles in the ecosystem. Otherwise, you need to be aware of the DIDs of the organisations you are seeking to authorize.

Generally, the Root Authorization also contains the schemas and types of credentials that will be issued below in the trust chain.&#x20;

{% hint style="info" %}
Note that it is common for the `rootAuthorizationForTrustChain` to be self-issued, from the same issuer DID to subject DID, authorizing it to carry out other operations.
{% endhint %}

### 5.1 Verifiable Authorization for Trust Chain

Using POST [/trust-registry/accreditation/issue](https://studio-api.cheqd.net/swagger/#/Trust%20Registry/accredit-issue)

Use the following request format:

<details>

<summary>Request format for Verifiable Authorization for Trust Chain</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e", // This is the Root DID for the Trust Chain
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad", // This is the DID Authorized at a Root Level and may often be the same DID as the issuer
  "schemas": [ // enter the schemas you have created for your trust registry
    {
      "types": "AIAgentAuthorization",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020"
    },
    {
      "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020",
      "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAccreditationToAccredit"
        ]
      },
      {
       "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020",
       "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "VerifiableAccreditationToAttest"
        ]
      }
  ],
  "format": "jwt",
  "accreditationName": "TrustFrameworkPolicy",
  "trustFramework": "https://learn.cheqd.io/governance/start",
  "trustFrameworkId": "cheqd Governance Framework"
}
```

</details>

<details>

<summary>Response format for Verifiable Authorization for Trust Chain</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "type": [
    "VerifiableCredential",
    "VerifiableAuthorizationForTrustChain"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e"
  },
  "credentialSubject": {
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018",
        "type": "MuseumPassCredential"
      }
    ],
    "id": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad"
  },
  "issuanceDate": "2024-10-15T04:06:47.000Z",
  "termsOfUse": {
    "type": "TrustFrameworkPolicy",
    "trustFramework": "https://learn.cheqd.io/governance/start",
    "trustFrameworkId": "cheqd Governance Framework"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjBhNWI5NGQwLWE0MTctNDhlZC1hNmY1LTRhYmM5ZTk1ODg4ZD9yZXNvdXJjZU5hbWU9TXVzZXVtUGFzc0NyZWRlbnRpYWxTY2hlbWEmcmVzb3VyY2VUeXBlPUpzb25TY2hlbWFWYWxpZGF0b3IyMDE4IiwidHlwZSI6Ik11c2V1bVBhc3NDcmVkZW50aWFsIn1dfSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIiwidHJ1c3RGcmFtZXdvcmsiOiJodHRwczovL2xlYXJuLmNoZXFkLmlvL2dvdmVybmFuY2Uvc3RhcnQiLCJ0cnVzdEZyYW1ld29ya0lkIjoiY2hlcWQgR292ZXJuYW5jZSBGcmFtZXdvcmsifX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyODk2NTIwNywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.6dKE9-y2Id852onu1_WnD6aJnDtxgFZcjCbCfQ8MT1ACsHY8ox5jiKP4QUJNmhwesLidC99Qa0uyCrUhvHc2Bg"
  }
}
```

</details>

<table><thead><tr><th width="298">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"types"</td><td>Yes</td><td>The "types" of credential you are authorizing for your trust chain</td></tr><tr><td>"url"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"trustFramework"</td><td>Yes</td><td>A URL that points to an Ecosystem Governance Framework</td></tr><tr><td>"trustFrameworkId"</td><td>Yes</td><td>The name of the Ecosystem Governance Framework</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

You can use the API below to make this transaction, using the parameter 'authorize'.

{% openapi-operation spec="cheqd-studio-api" path="/trust-registry/accreditation/issue" method="post" %}
[OpenAPI cheqd-studio-api](https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json)
{% endopenapi-operation %}

## Step 6: Issue your next Accreditation

Depending on how many layers deep you want your trust registry, you now need to issue an `accreditationToAccredit` or an `accreditationToAttest.` In essence, you need to decide whether you want to accredit a subordinate entity to accredit other organisations (creating a deeper trust chain), or accredit a subordinate entity to issue Credentials to your AI Agent.

### 6.1 Verifiable Accreditation to Accredit

<details>

<summary>Request format for Verifiable Accreditation to Accredit</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad",
  "schemas": [
    {
      "types": "AIAgentAuthorization",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020"
    },
    {
      "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020",
      "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAccreditationToAccredit"
        ]
      },
      {
       "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020",
       "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "VerifiableAccreditationToAttest"
        ]
      }
  ],
  "format": "jwt",
  "accreditationName": "accreditationToAttestTest",
  "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
  "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorizationForTrustChain",
}
```

</details>

<details>

<summary>Response format for Verifiable Accreditation to Accredit</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "type": [
    "VerifiableCredential",
    "VerifiableAccreditationToAccredit"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e"
  },
  "credentialSubject": {
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018",
        "type": "AIAgentAuthorization"
      }
    ],
    "id": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad"
  },
  "issuanceDate": "2024-10-15T04:06:47.000Z",
  "termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
    "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorizationForTrustChain",
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjBhNWI5NGQwLWE0MTctNDhlZC1hNmY1LTRhYmM5ZTk1ODg4ZD9yZXNvdXJjZU5hbWU9TXVzZXVtUGFzc0NyZWRlbnRpYWxTY2hlbWEmcmVzb3VyY2VUeXBlPUpzb25TY2hlbWFWYWxpZGF0b3IyMDE4IiwidHlwZSI6Ik11c2V1bVBhc3NDcmVkZW50aWFsIn1dfSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIiwidHJ1c3RGcmFtZXdvcmsiOiJodHRwczovL2xlYXJuLmNoZXFkLmlvL2dvdmVybmFuY2Uvc3RhcnQiLCJ0cnVzdEZyYW1ld29ya0lkIjoiY2hlcWQgR292ZXJuYW5jZSBGcmFtZXdvcmsifX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyODk2NTIwNywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.6dKE9-y2Id852onu1_WnD6aJnDtxgFZcjCbCfQ8MT1ACsHY8ox5jiKP4QUJNmhwesLidC99Qa0uyCrUhvHc2Bg"
  }
}
```

</details>

<table><thead><tr><th width="299">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"url"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"types"</td><td>Yes</td><td>The types of credential the subject is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"parentAccreditation"</td><td>Yes</td><td>A URL or DID URL of Accreditation of the Issuer  demonstrating capacity to issue this Accreditation.</td></tr><tr><td>"rootAuthorization"</td><td>Yes</td><td>A URL or DID URL of the root authorization governing the ecosystem</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr></tbody></table>

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

### 6.2 Verifiable Accreditation to Attest

If you just want to accredit the subordinate DID to issue credentials to your AI Agent, use a Verifiable Accreditation to Attest.&#x20;

<details>

<summary>Request format for Verifiable Accreditation to Attest</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad",
  "schemas": [
    {
      "types": "AIAgentAuthorization",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020"
    },
    {
      "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAccreditation&resourceType=JSONSchemaValidator2020",
      "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAccreditationToAccredit"
        ]
      },
      {
       "uri": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=VerifiableAttestation&resourceType=JSONSchemaValidator2020",
       "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "VerifiableAccreditationToAttest"
        ]
      }
  ],
  "format": "jwt",
  "accreditationName": "accreditationToAttestTest",
  "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
  "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorizationForTrustChain",
}
```

</details>

<details>

<summary>Response format for Verifiable Accreditation to Attest</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "type": [
    "VerifiableCredential",
    "VerifiableAccreditationToAccredit"
  ],
  "issuer": {
    "id": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e"
  },
  "credentialSubject": {
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018",
        "type": "MuseumPassCredential"
      }
    ],
    "id": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad"
  },
  "issuanceDate": "2024-10-15T04:06:47.000Z",
  "termsOfUse": {
    "type": "VerifiableAuthorizationForTrustChain",
    "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
    "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorizationForTrustChain",
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjBhNWI5NGQwLWE0MTctNDhlZC1hNmY1LTRhYmM5ZTk1ODg4ZD9yZXNvdXJjZU5hbWU9TXVzZXVtUGFzc0NyZWRlbnRpYWxTY2hlbWEmcmVzb3VyY2VUeXBlPUpzb25TY2hlbWFWYWxpZGF0b3IyMDE4IiwidHlwZSI6Ik11c2V1bVBhc3NDcmVkZW50aWFsIn1dfSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIiwidHJ1c3RGcmFtZXdvcmsiOiJodHRwczovL2xlYXJuLmNoZXFkLmlvL2dvdmVybmFuY2Uvc3RhcnQiLCJ0cnVzdEZyYW1ld29ya0lkIjoiY2hlcWQgR292ZXJuYW5jZSBGcmFtZXdvcmsifX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyODk2NTIwNywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.6dKE9-y2Id852onu1_WnD6aJnDtxgFZcjCbCfQ8MT1ACsHY8ox5jiKP4QUJNmhwesLidC99Qa0uyCrUhvHc2Bg"
  }
}
```

</details>

<table><thead><tr><th width="299">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"url"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"types"</td><td>Yes</td><td>The types of credential the subject is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"parentAccreditation"</td><td>Yes</td><td>A URL or DID URL of Accreditation of the Issuer  demonstrating capacity to issue this Accreditation.</td></tr><tr><td>"rootAuthorization"</td><td>Yes</td><td>A URL or DID URL of the root authorization governing the ecosystem</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr></tbody></table>

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

{% hint style="info" %}
Note that there MUST be an `accreditationToAttest` for credentials to be issued that reference an accreditation in the next tutorial.&#x20;
{% endhint %}

## Core Trust Registry Setup Complete!

Great! Now you have set up the core functionality for your trust registry. Next you will want to issue a Verifiable Attestation from the "trusted issuer" in the Trust Registry to an AI Agent:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Credentials to AI Agent</strong></mark></td><td>Issue a Verifiable Credential to your AI Agent referencing the <code>parentAccreditation</code> and <code>rootAuthorization</code> for the trust chain.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr></tbody></table>
