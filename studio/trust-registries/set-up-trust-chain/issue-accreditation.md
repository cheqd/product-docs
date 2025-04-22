---
description: Issue Verifiable Accreditations as DID-Linked Resources.
---

# Issue Verifiable Accreditation

Users are able to **issue Verifiable Accreditations as DID-Linked Resources on-ledger**, which may be used to **verify whether a particular recipient of an accreditation is accredited to issue a certain type of credential**, under the scope of a particular governance framework. This implementation on cheqd builds on the principles of the EBSI Trust Chain model, using DID-Linked Resources to create a more standardised format for storing, retrieving and dereferencing to trust registry entries.

## Step 1: Set up your account

Make sure you have set up your account with **cheqd Studio** and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with <strong>cheqd Studio</strong> and log in to start using the APIs.</td><td><a href="../../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a DID

Before you can create a Verifiable Accreditation, you need to create a DID which is used to link to one or multiple Verifiable Accreditations on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Choose the type of Accreditation

Verifiable Accreditations are JSON objects that take the form of the Verifiable Credential data model. There are three types of Verifiable Accreditation:

<table><thead><tr><th width="299">Type</th><th>Description</th></tr></thead><tbody><tr><td>Verifiable Authorisation for Trust Chain</td><td>This Accreditation authorises the recipient to issue Accreditations with reference to a particular governance framework.</td></tr><tr><td>Verifiable Accreditation to Accredit</td><td>This Accreditation verifies that an organisation has the permissions needed to <strong>accredit other organisations</strong> for issuing a particular type of Verifiable Accredittion.</td></tr><tr><td>Verifiable Accreditation to Attest</td><td>This Accreditation verifies that an organisation has the permissions needed to <strong>issue</strong> Verifiable Credentials, defined by a particular schema.</td></tr></tbody></table>

## Step 4: Compile the appropriate request format for the API

For each accreditation type, the user will need to use a different request format for the API.&#x20;

### Verifiable Authorisation for Trust Chain

<details>

<summary>Request format for Verifiable Authorisation for Trust Chain</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad",
  "schemas": [
    {
      "type": "MuseumPassCredential",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018"
    }
  ],
  "format": "jwt",
  "accreditationName": "authoriseAccreditationTest",
  "trustFramework": "https://learn.cheqd.io/governance/start",
  "trustFrameworkId": "cheqd Governance Framework"
}
```

</details>

<details>

<summary>Response format for Verifiable Authorisation for Trust Chain</summary>

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "type": [
    "VerifiableCredential",
    "VerifiableAuthorisationForTrustChain"
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
    "type": "VerifiableAuthorisationForTrustChain",
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

<table><thead><tr><th width="298">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"schemas"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"trustFramework"</td><td>Yes</td><td>A URL that points to an Ecosystem Governance Framework</td></tr><tr><td>"trustFrameworkId"</td><td>Yes</td><td>The name of the Ecosystem Governance Framework</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr></tbody></table>

### Verifiable Accreditation to Accredit

<details>

<summary>Request format for Verifiable Accreditation to Accredit</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad",
  "schemas": [
    {
      "type": "MuseumPassCredential",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018"
    }
  ],
  "format": "jwt",
  "accreditationName": "accreditationToAttestTest",
  "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
  "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorisationForTrustChain",
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
        "type": "MuseumPassCredential"
      }
    ],
    "id": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad"
  },
  "issuanceDate": "2024-10-15T04:06:47.000Z",
  "termsOfUse": {
    "type": "VerifiableAuthorisationForTrustChain",
    "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
    "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorisationForTrustChain",
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjBhNWI5NGQwLWE0MTctNDhlZC1hNmY1LTRhYmM5ZTk1ODg4ZD9yZXNvdXJjZU5hbWU9TXVzZXVtUGFzc0NyZWRlbnRpYWxTY2hlbWEmcmVzb3VyY2VUeXBlPUpzb25TY2hlbWFWYWxpZGF0b3IyMDE4IiwidHlwZSI6Ik11c2V1bVBhc3NDcmVkZW50aWFsIn1dfSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIiwidHJ1c3RGcmFtZXdvcmsiOiJodHRwczovL2xlYXJuLmNoZXFkLmlvL2dvdmVybmFuY2Uvc3RhcnQiLCJ0cnVzdEZyYW1ld29ya0lkIjoiY2hlcWQgR292ZXJuYW5jZSBGcmFtZXdvcmsifX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyODk2NTIwNywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.6dKE9-y2Id852onu1_WnD6aJnDtxgFZcjCbCfQ8MT1ACsHY8ox5jiKP4QUJNmhwesLidC99Qa0uyCrUhvHc2Bg"
  }
}
```

</details>

<table><thead><tr><th width="299">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"schemas"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"parentAccreditation"</td><td>Yes</td><td>A URL or DID URL of Accreditation of the Issuer  demonstrating capacity to issue this Accreditation.</td></tr><tr><td>"rootAuthorization"</td><td>Yes</td><td>A URL or DID URL of the root authorization governing the ecosystem</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr></tbody></table>

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

### Verifiable Accreditation to Attest

<details>

<summary>Request format for Verifiable Accreditation to Attest</summary>

```json
{
  "issuerDid": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "subjectDid": "did:cheqd:testnet:6af412d7-2f04-4e12-a424-e6719db487ad",
  "schemas": [
    {
      "type": "MuseumPassCredential",
      "url": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=MuseumPassCredentialSchema&resourceType=JsonSchemaValidator2018"
    }
  ],
  "format": "jwt",
  "accreditationName": "accreditationToAttestTest",
  "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
  "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorisationForTrustChain",
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
    "type": "VerifiableAuthorisationForTrustChain",
    "parentAccreditation": "did:cheqd:testnet:15b74787-6e48-4fd5-8020-eab24e990578?resourceName=accreditAccreditation&resourceType=VerifiableAccreditationToAccredit",
    "rootAuthorization": "did:cheqd:testnet:5RpEg66jhhbmASWPXJRWrA?resourceName=authorizeAccreditation&resourceType=VerifiableAuthorisationForTrustChain",
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImFjY3JlZGl0ZWRGb3IiOlt7InNjaGVtYUlkIjoiaHR0cHM6Ly9yZXNvbHZlci5jaGVxZC5uZXQvMS4wL2lkZW50aWZpZXJzL2RpZDpjaGVxZDp0ZXN0bmV0OjBhNWI5NGQwLWE0MTctNDhlZC1hNmY1LTRhYmM5ZTk1ODg4ZD9yZXNvdXJjZU5hbWU9TXVzZXVtUGFzc0NyZWRlbnRpYWxTY2hlbWEmcmVzb3VyY2VUeXBlPUpzb25TY2hlbWFWYWxpZGF0b3IyMDE4IiwidHlwZSI6Ik11c2V1bVBhc3NDcmVkZW50aWFsIn1dfSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiVmVyaWZpYWJsZUF1dGhvcmlzYXRpb25Gb3JUcnVzdENoYWluIiwidHJ1c3RGcmFtZXdvcmsiOiJodHRwczovL2xlYXJuLmNoZXFkLmlvL2dvdmVybmFuY2Uvc3RhcnQiLCJ0cnVzdEZyYW1ld29ya0lkIjoiY2hlcWQgR292ZXJuYW5jZSBGcmFtZXdvcmsifX0sInN1YiI6ImRpZDpjaGVxZDp0ZXN0bmV0OjZhZjQxMmQ3LTJmMDQtNGUxMi1hNDI0LWU2NzE5ZGI0ODdhZCIsIm5iZiI6MTcyODk2NTIwNywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6YjAwM2RmNmYtZWM4ZS00OGRkLTlhMmItNzAxMWM1Y2YwYTVlIn0.6dKE9-y2Id852onu1_WnD6aJnDtxgFZcjCbCfQ8MT1ACsHY8ox5jiKP4QUJNmhwesLidC99Qa0uyCrUhvHc2Bg"
  }
}
```

</details>

<table><thead><tr><th width="299">Request Parameter</th><th>Required</th><th>Description</th></tr></thead><tbody><tr><td>"issuerDid"</td><td>Yes</td><td>The DID of the Issuer of the Accreditation</td></tr><tr><td>"subjectDid"</td><td>Yes</td><td>The DID of the Recipient of the Accreditation</td></tr><tr><td>"schemas"</td><td>Yes</td><td>A schema or multiple schemas that the recipient is accredited to issue</td></tr><tr><td>"format"</td><td>Optional</td><td>Defaults to "jwt" but may also be "json-ld"</td></tr><tr><td>"accreditationName"</td><td>Yes</td><td>Name of the accreditation which is used for chronological versioning of the accreditation.</td></tr><tr><td>"parentAccreditation"</td><td>Yes</td><td>A URL or DID URL of Accreditation of the Issuer  demonstrating capacity to issue this Accreditation.</td></tr><tr><td>"rootAuthorization"</td><td>Yes</td><td>A URL or DID URL of the root authorization governing the ecosystem</td></tr><tr><td>"credentialStatus"</td><td>Optional</td><td>An object detailing the status information of the Accreditation </td></tr></tbody></table>

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

## Step 5: Make request to the API

{% openapi src="https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json" path="/trust-registry/accreditation/issue" method="post" %}
[https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json)
{% endopenapi %}

## Step 7: Reference the Verifiable Accreditation

Owing to the design of DID-Linked Resources, following the creation of the a Verifiable Accreditation, users are able to reference the **specific version**, or create a query to **always fetch the latest version** of the Accreditation.&#x20;

Using a DID Resolver or the search DID endpoint, users can find the DID URL and unique resourceId of the Verifiable Accreditation. The unique resourceId allows users to specify this exact version of the Accreditation.

In the DID Document Metadata, users should find "linkedResourceMetadata", like the following snippet:

```json
"linkedResourceMetadata": [
      {
        "resourceURI": "did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d/resources/4e1104f9-2ee9-4bde-adc2-ab8ba72b124a",
        "resourceCollectionId": "0a5b94d0-a417-48ed-a6f5-4abc9e95888d",
        "resourceId": "4e1104f9-2ee9-4bde-adc2-ab8ba72b124a",
        "resourceName": "OxfordUniversityAccreditation",
        "resourceType": "VerifiableAccreditationToAccredit",
        "mediaType": "application/json",
        "resourceVersion": "",
        "created": "2023-03-24T12:13:45Z",
        "checksum": "6819aaecd4073173b159fedf8077c38e14939d03d58e7f4e2a0ddfe034eb2ed4",
        "previousVersionId": null,
        "nextVersionId": null
      } 
```

### Specific version of the Verifiable Accreditation

Here, the "`resourceURI`" specifies the DID URL of the specific Verifiable Accreditation that was created.

### Latest version of the Verifiable Accreditation

In order to **reference the latest version of the Verifiable Accreditation**, the following construction needs to be used:

`did:cheqd:<namespace>:<resourceCollectionId>?resourceName=<resourceName>&resourceType=<resourceType>`

For example:

`did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=OxfordUniversityAccreditation&resourceType=VerifiableAccreditationToAccredit`

### Verifiable Accreditation at specific point in time

In order to **reference the Verifiable Accreditation at a particular point in time**, the following construction needs to be used:

`did:cheqd:<namespace>:<resourceCollectionId>?resourceName=<resourceName>&resourceType=<resourceType>&resourceVerionTime=<XMLDateTime>`

For example:

`did:cheqd:testnet:0a5b94d0-a417-48ed-a6f5-4abc9e95888d?resourceName=OxfordUniversityAccreditation&resourceType=VerifiableAccreditationToAccredit&resourceVersionTime=2023-02-22T06:58:18.61Z`
