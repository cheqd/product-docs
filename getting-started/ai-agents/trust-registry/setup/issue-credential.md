---
description: Issue a Credential to an AI Agent using cheqd's APIs.
---

# Issue Verifiable Credentials to AI Agent

Once you have created your Trust Registry for the accreditation and authorization of the organisations in your ecosystem, you can issue a credential to your AI Agent.&#x20;

## Step 1: Create DID for AI Agent

Each AI Agent must have its own DID to securely receive and manage credentials and accreditations. This identification allows the AI Agent to cryptographically control these credentials and accreditations.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create AI Agent DID</strong></mark></td><td>Create a W3C conformant DID on cheqd using our MCP Server.</td><td><a href="../setup-mcp/create-did.md">create-did.md</a></td></tr></tbody></table>

## Step 2: Choose Schema for Credential

As described in [the previous tutorial](../), you should have already designed and published schemas for your AI Agent ecosystem.&#x20;

You must decide which schema to use for the credential you are issuing to the AI Agent. You should make sure that you, as an issuer, are `accreditedToAttest` for the specific credential schema you want to use.&#x20;

## Step 3: Compile Credential Request

To ensure the AI Agent's credentials are demonstrably verifiable to a root of trust, certain steps are essential in the credential issuance process.

{% hint style="success" %}
For `subjectDid`  use the DID created in Step 1.
{% endhint %}

An example of the request format is below:

```json
{
  "issuerDid": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771", // DID of Trusted Issuer
  "subjectDid": "did:cheqd:testnet:a7e8bf7c-9d97-4b1e-a7bb-43a6754aafbf", // DID of AI Agent
  "attributes": {
      "aiAgentName": "ChatGPT-4 Turbo",
      "aiAgentVersion": "4.0-turbo",
      "model": "GPT-4 Turbo",
      "modelVersion": "4.0",
      "contextWindow": 128000,
      "temperature": 0.7,
      "topK": 40,
      "topP": 0.9,
      "maxTokens": 4096,
      "fineTuned": false,
      "fineTuningDetails": null,
      "safetyRating": "ISO 42001 Certified",
      "evaluationMetrics": [
        "BLEU-4",
        "ROUGE-L",
        "F1 Score: 92.5%"
      ],
      "certificationAuthority": "AI Ethics Board",
      "validUntil": "2026-03-28T12:00:00Z"
  },
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "AIAgentAuthorization"
  ],
  "format": "jwt",
  "credentialSchema": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020",
    "termsOfUse": {
      "type": "AttestationPolicy",
      "parentAccreditation": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771?resourceName=AccreditationToAttest&resourceType=VerifiableAccreditationToAttest",
      "rootAuthorization": "did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=OrganisationAuthorizationForAIAgents&resourceType=VerifiableAuthorizationForTrustChain"
    }
}
```

The table below breaks down what components are required and why they are needed:

| Field               | Required? | Description                                                                              |
| ------------------- | --------- | ---------------------------------------------------------------------------------------- |
| `issuerDid`         | Yes       | The Issuer DID issuing the credential to the AI Agent                                    |
| `subjectDid`        | Yes       | The DID of the AI Agent                                                                  |
| `attributes`        | Yes       | The specific permissions, scopes or attributes you want to issue to the AI Agent         |
| `type`              | Yes       | The type of credential. This should match the types the issuer is accredited to issue,   |
| `format`            | No        | Defaults to VC JWT                                                                       |
| `credentialSchema`  | Yes       | The credential schema matching the attributes of the credential                          |
| `termsOfUse`        | Yes       | Pointers to the accreditations of the issuer                                             |
| `type`              | Yes       | Must be `attestationPolicy`                                                              |
| parentAccreditation | Yes       | Must point to the accreditation of the Issuer, matching the credential type and schema   |
| rootAuthorization   | Yes       | Must point to the root authorization that has accredited DIDs higher in the trust chain. |

## Step 4: Issue Verifiable Credential to AI Agent

Issue the compiled credential using the cheqd studio API enpoint below:

{% openapi-operation spec="cheqd-studio-api" path="/credential/issue" method="post" %}
[OpenAPI cheqd-studio-api](https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json)
{% endopenapi-operation %}

## (Optional) Step 5: Publish Credential Response as a DID-Linked Resource

The Issuer, after issuing the credential to the AI Agent, _may_ publish the response (the fully formatted verifiable credential) as a DID-Linked Resource.

The response format should look like the following example, including a proof (signed by the issuer).

```json
{
  "credentialSubject": {
    "aiAgentName": "ChatGPT-4 Turbo",
    "aiAgentVersion": "4.0-turbo",
    "model": "GPT-4 Turbo",
    "modelVersion": "4.0",
    "contextWindow": 128000,
    "temperature": 0.7,
    "topK": 40,
    "topP": 0.9,
    "maxTokens": 4096,
    "fineTuned": false,
    "fineTuningDetails": null,
    "safetyRating": "ISO 42001 Certified",
    "evaluationMetrics": [
      "BLEU-4",
      "ROUGE-L",
      "F1 Score: 92.5%"
    ],
    "certificationAuthority": "AI Ethics Board",
    "validUntil": "2026-03-28T12:00:00Z",
    "id": "did:cheqd:testnet:a7e8bf7c-9d97-4b1e-a7bb-43a6754aafbf"
  },
  "issuer": {
    "id": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771"
  },
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "AIAgentAuthorization"
  ],
  "termsOfUse": {
    "type": "AttestationPolicy",
    "parentAccreditation": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771?resourceName=AccreditationToAttest&resourceType=VerifiableAccreditationToAttest",
    "rootAuthorization": "did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=OrganisationAuthorizationForAIAgents&resourceType=VerifiableAuthorizationForTrustChain"
  },
  "credentialSchema": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020",
  "@context": [
    "https://www.w3.org/2018/credentials/v1"
  ],
  "issuanceDate": "2025-04-04T02:43:52.000Z",
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiVmVyaWZpYWJsZUF0dGVzdGF0aW9uIiwiQUlBZ2VudEF1dGhvcmlzYXRpb24iXSwiY3JlZGVudGlhbFN1YmplY3QiOnsiYWlBZ2VudE5hbWUiOiJDaGF0R1BULTQgVHVyYm8iLCJhaUFnZW50VmVyc2lvbiI6IjQuMC10dXJibyIsIm1vZGVsIjoiR1BULTQgVHVyYm8iLCJtb2RlbFZlcnNpb24iOiI0LjAiLCJjb250ZXh0V2luZG93IjoxMjgwMDAsInRlbXBlcmF0dXJlIjowLjcsInRvcEsiOjQwLCJ0b3BQIjowLjksIm1heFRva2VucyI6NDA5NiwiZmluZVR1bmVkIjpmYWxzZSwiZmluZVR1bmluZ0RldGFpbHMiOm51bGwsInNhZmV0eVJhdGluZyI6IklTTyA0MjAwMSBDZXJ0aWZpZWQiLCJldmFsdWF0aW9uTWV0cmljcyI6WyJCTEVVLTQiLCJST1VHRS1MIiwiRjEgU2NvcmU6IDkyLjUlIl0sImNlcnRpZmljYXRpb25BdXRob3JpdHkiOiJBSSBFdGhpY3MgQm9hcmQiLCJ2YWxpZFVudGlsIjoiMjAyNi0wMy0yOFQxMjowMDowMFoifSwidGVybXNPZlVzZSI6eyJ0eXBlIjoiQXR0ZXN0YXRpb25Qb2xpY3kiLCJwYXJlbnRBY2NyZWRpdGF0aW9uIjoiZGlkOmNoZXFkOnRlc3RuZXQ6MGEzNWQ1NTktMDBmZi00MWI2LTgxYWQtZjY0ZmFhNTIyNzcxP3Jlc291cmNlTmFtZT1BY2NyZWRpdGF0aW9uVG9BdHRlc3QmcmVzb3VyY2VUeXBlPVZlcmlmaWFibGVBY2NyZWRpdGF0aW9uVG9BdHRlc3QiLCJyb290QXV0aG9yaXNhdGlvbiI6ImRpZDpjaGVxZDp0ZXN0bmV0OmM2NjMwZjFlLTkyNDgtNGFmNi1iN2FjLTViY2FmNjQ2ZjIxMz9yZXNvdXJjZU5hbWU9T3JnYW5pc2F0aW9uQXV0aG9yaXNhdGlvbkZvckFJQWdlbnRzJnJlc291cmNlVHlwZT1WZXJpZmlhYmxlQXV0aG9yaXNhdGlvbkZvclRydXN0Q2hhaW4ifSwiY3JlZGVudGlhbFNjaGVtYSI6Imh0dHBzOi8vcmVzb2x2ZXIuY2hlcWQubmV0LzEuMC9pZGVudGlmaWVycy9kaWQ6Y2hlcWQ6dGVzdG5ldDpjNjYzMGYxZS05MjQ4LTRhZjYtYjdhYy01YmNhZjY0NmYyMTM_cmVzb3VyY2VOYW1lPUFJQWdlbnRBdXRob3Jpc2F0aW9uJnJlc291cmNlVHlwZT1KU09OU2NoZW1hVmFsaWRhdG9yMjAyMCJ9LCJzdWIiOiJkaWQ6Y2hlcWQ6dGVzdG5ldDphN2U4YmY3Yy05ZDk3LTRiMWUtYTdiYi00M2E2NzU0YWFmYmYiLCJuYmYiOjE3NDM3MzQ2MzIsImlzcyI6ImRpZDpjaGVxZDp0ZXN0bmV0OjBhMzVkNTU5LTAwZmYtNDFiNi04MWFkLWY2NGZhYTUyMjc3MSJ9.z9xTp5dSMACTLhrAsO-RBjcmaJJvWHqD6_78FTjaOkBroAMS0f8NlvpIrC7CGojkzCv_T8M_VBexzXU9I8JKAg"
  }
}
```

You can follow the tutorial below to publish your issued credential as a DID-Linked Resource. Generally we suggest the `resourceType` of `VerifiableAttestation` for credentials issued to AI Agents.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resource</strong></mark></td><td>Link resources such as schemas to your DID, publishing as a DID-Linked Resource.</td><td><a href="../../../../studio/did-linked-resources/">did-linked-resources</a></td></tr></tbody></table>

This will store the schemas securely on the cheqd Network, where they can be fetched using DID URLs.

### Step 5: Provision Verifiable Credential into AI Agent using MCP server

To complete the setup of your AI Agent trust registry and its associated credential, the final step is to provision this credential directly into the AI Agent's wallet using our MCP Server. Follow the tutorial below to finalize your setup!

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Provision Credential to AI Agent</strong></mark></td><td>Once you have issued your credential and have the JWT, you can provision this credential directly to the AI Agent using our MCP server.</td><td><a href="../setup-mcp/import-credential.md">import-credential.md</a></td></tr></tbody></table>
