---
description: Issue Verifiable Accreditations as DID-Linked Resources
---

# Create Verifiable Accreditation

Users are able to **issue Verifiable Accreditations as DID-Linked Resources on-ledger**, which may be used to **verify whether a particular recipient of an accreditation is accredited to issue a certain type of credential**, under the scope of a particular governance framework. This implementation on cheqd builds on the principles of the EBSI Trust Chain model, using DID-Linked Resources to create a more standardised format for storing, retrieving and dereferencing to trust registry entries.

## Step 1: Set up your account

Make sure you have set up your account with cheqd Studio and are logged in, using our guide below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up your account</strong></mark></td><td>Set up your account with cheqd Studio and log in to start using the APIs.</td><td><a href="../../getting-started/studio/set-up-account.md">set-up-account.md</a></td></tr></tbody></table>

## Step 2: Create a DID

Before you can create a Verifiable Accreditation, you need to create a DID which is used to link to one or multiple Verifiable Accreditations on-ledger. Use the API in the page below to create a DID:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create an Issuer DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using the <code>did:cheqd</code> DID Method.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 3: Create your Verifiable Accreditation body and save the file locally

Verifiable Accreditations are JSON objects that take the form of the Verifiable Credential data model. There are two types of Verifiable Accreditation:

<table><thead><tr><th width="299">Type</th><th>Description</th></tr></thead><tbody><tr><td>Verifiable Accreditation to Accredit</td><td>This Credential verifies that an organisation has the permissions needed to accredit other organisations for issuing a particular type of Verifiable Accredittion.</td></tr><tr><td>Verifiable Accreditation to Attest</td><td>This Credential verifies that an organisation has the permissions needed to issue Verifiable Credentials, defined by a particular schema.</td></tr></tbody></table>

For a trusted ecosystem, these attestations are required to trace the legitimacy of a credential issuer to a root-of-trust.&#x20;

<details>

<summary>Example of Verifiable Accreditation to Accredit</summary>

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "id": "urn:uuid:8568b525-a24e-4bc0-9d97-6a8459ec0130",
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "VerifiableAccreditation",
    "VerifiableAccreditationToAccredit"
  ],
  "issuer": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
  "issuanceDate": "2021-11-01T00:00:00Z",
  "validFrom": "2021-11-01T00:00:00Z",
  "expirationDate": "2025-06-22T14:11:44Z",
  "issued": "2020-06-22T14:11:44Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:e21b63d1-a771-4eb9-9452-869cd30fd622",
    "accreditedFor": [
      {
        "schemaId": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/83eb0ed8-37d1-4ba6-9e0b-40d60676d4aa",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "DiplomaCredential"
        ]
      }
    ]
  },
  "credentialStatus": {
     "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6?resourceName=accreditationStatus&resourceType=StatusList2021Revocation",
     "type": "StatusList2021Revocation"
  },
  "termsOfUse": [
    {
      "id": "https://example.com/governance-framework/../..xyz",
      "type": "GovernanceFramework"
    }
  ],
  "credentialSchema": [
    {
      "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/83eb0ed8-37d1-4ba6-9e0b-40d60676d4aa",
      "type": "FullJsonSchemaValidator2021"
    }
  ]
}


```

</details>

<details>

<summary>Verifiable Accreditation to Attest Example</summary>

```json
{
  "@context": ["https://www.w3.org/2018/credentials/v1"],
  "id": "urn:uuid:8568b525-a24e-4bc0-9d97-6a8459ec0130",
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "VerifiableAccreditation",
    "VerifiableAccreditationToAttest"
  ],
  "issuer": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6",
  "issuanceDate": "2021-11-01T00:00:00Z",
  "validFrom": "2021-11-01T00:00:00Z",
  "expirationDate": "2025-06-22T14:11:44Z",
  "issued": "2020-06-22T14:11:44Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:e21b63d1-a771-4eb9-9452-869cd30fd622",
    "reservedAttributeId": "15b49499-2a36-4c73-9f5b-7409b44ce7a3",
    "accreditedFor": [
      {
        "schemaId": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
        "types": [
          "VerifiableCredential",
          "VerifiableAttestation",
          "DiplomaCredential"
        ]
    ]
  },
  "credentialStatus": {
     "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6?resourceName=accreditationStatus&resourceType=StatusList2021Revocation",
     "type": "StatusList2021Revocation"
  },
  "termsOfUse": {
    "type": "AccreditationPolicy",
    "parentAccreditation": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
    "policyId": "https://example.com/policies/124",
    "rootAuthorisation": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
    "trustFramework": "Name of the Governance Framework (GF)",
    "lib": "urn:professional-qualifications-directive"
  },
  "credentialSchema": [
    {
      "id": "did:cheqd:testnet:098c4f66-b461-4037-9cf0-c5db75b270c6/resources/da4159f1-ff50-4a7c-b0cb-40d3a1f71003a",
      "type": "FullJsonSchemaValidator2021"
    }
  ]
}


```

</details>

Save this file locally and call it something like `verifiableAccreditation.json`

{% hint style="info" %}
Note that each SON file must be **below \~45kb in size**.
{% endhint %}

## Step 4: Encode the file

Prepare a file with resource and encode it into `base64, base64url` or `hex`. On Unix systems, you can use the following command input:

```bash
$ base64 -w 0 resource.json
<path-to-the-resource-file>
```

Expected output:

```bash
$ base64 -w 0 resource.json
SGVsbG8sIHdvcmxk
```

## Step 5: Set the resource name and type

DID-Linked Resources are grouped by having identical **names** and **types**. This means if you want to create a new version of the same Resource, you will need to **specify the same name and type in the following request**.&#x20;

For Verifiable Accreditations, the "type" MUST be either:  **`"VerifiableAccreditationToAccredit"`** or **`"VerifiableAccreditationToAccredit"`**

For example:

```json
{
    "data": "SGVsbG8sIHdvcmxk",
    "encoding": "base64url",
    "name": "OxfordUniversityAccreditation",
    "type": "VerifiableAccreditationToAccredit"
}
```

## Step 6: Populate the request inputs and hit the API

Ensure that you link this Verifiable Accreditation to the DID that you created in step 3. This will sign the resource with the same verification method keys in your DID Document, ensuring cryptographic integrity and Controllership of the Status List.

As a DID-Linked Resource, the Verifiable Accreditation will have a **fully resolvable DID URL** which can be referenced within the body of Verifiable Credentials, and queried by verification policies to establish the status of the specific credential.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/resource/create/{did}" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

## Step 7: Reference the Verifiable Accreditation

Owing to the design of DID-Linked Resources, following the creation of the a Verifiable Accreditation, users are able to reference the **specific version**, or create a query to **always fetch the latest version** of the Accreditation.&#x20;

Using a DID Resolver or the search DID endpoint, users can find the DID URL and unique resourceId of the Verifiable Accreditation. The unique resourceId allows users to specify this exact version of the Accreditation.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/did/search/{did}" method="get" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}

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
