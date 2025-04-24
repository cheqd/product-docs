---
description: Verify and validate your AI Agent's trust with TRAIN
---

# Validate AI Agent Trust Chain

We have built a Trust Registry validation tool to allow AI Agents to demonstrate that they are trustworthy and have been created by accredited and authorised organisations.

This validation can be carried out over REST API. Find the link to the APIs here:

{% embed url="https://dev-train.trust-scheme.de/swagger_cheqd_train/#/TrustedContentResolver/resolveCheqdCredential" %}

## Resolve cheqd API

The Resolve cheqd API can be used to input information from a Verifiable Credential, issued in the previous tutorial and query whether the Issuer DID is valid, and whether it is accredited to issue the type and schema of credential - traceable all the way back to a Root of Trust.

<details>

<summary>Example Request Format</summary>

```json
{
  "issuer": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771",
  "type": [
    "VerifiableCredential",
    "VerifiableAttestation",
    "AIAgentAuthorisation"
  ],
  "termsofuse": "AttestationPolicy",
  "parentAccreditation": "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771?resourceName=AccreditationToAttest&resourceType=VerifiableAccreditationToAttest",
  "credentialSchema": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213?resourceName=AIAgentAuthorisation&resourceType=JSONSchemaValidator2020"
}
```

</details>

The request format above shows how to format a request based on a verifiable credential issued in the previous tutorial.&#x20;

{% hint style="info" %}
This validation tool includes extra validation for checking Root DIDs against DNS records, which we will describe in a seperate tutorial.
{% endhint %}

<details>

<summary>Example Response Format</summary>

```json
{
  "VerificationStatus": false,
  "VerificationResult": {
    "AccreditorDIDs": [
      "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771",
      "did:cheqd:testnet:0a35d559-00ff-41b6-81ad-f64faa522771",
      "did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213"
    ],
    "FoundRootIssuerDID": "did:cheqd:testnet:c6630f1e-9248-4af6-b7ac-5bcaf646f213",
    "TrustFramework": "https://medium.com/quantum-economics/why-we-started-the-decentralized-ai-agent-alliance-6eb0938d7bc5",
    "TrustFrameworkId": "DAIAA Governance Framework",
    "FindingCorrespondingDNSTrustFrameworkInitiated": true,
    "VerifyRootIssuerDIDinDNS": false
  }
}
```

</details>

If the validation tool is able to find a `RootIssuerDID`, `TrustFramework` and `TrustFrameworkId`, then the AI Agent's issuer is accredited properly and the AI Agent's attributes can be trusted.&#x20;
