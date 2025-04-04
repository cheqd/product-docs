---
description: Design and Build Trust Registries for AI Agents
---

# 💡 Build Trust for AI Agents

## Trust Registries for AI Agents <a href="#hierarchy-examples" id="hierarchy-examples"></a>

Trust Registries play a critical role in establishing trust for AI Agents. Acting as authoritative databases, they can verify the AI Agent is accredited and authorised to carry out certain actions, ensuring transparency and accountability.

Here’s how they support the AI ecosystem:

* **Accreditation of AI Agents**: Trust Registries maintain verified records of AI agents, ensuring that they are accredited by trusted organisations or ecosystems. Through accreditations, these trust registries validate that AI agents are legitimate and scope the permissions under which the AI agent can operate within.
* **Transparency**: Trust Registries enable users and organisations to query and verify the origins and accreditations of AI systems they engage with. By providing a publicly accessible record of trusted AI agents, Trust Registries empower stakeholders to assess the credibility and history of an AI system before utilising it. This enhances confidence in the system, especially when the AI’s decisions impact sensitive areas like personal data or legal outcomes.
* **Governance**: Trust Registries also serve as a governance tool, ensuring that AI developers and platforms are held accountable for their actions. By maintaining a registry of accredited AI systems, these registries can track the ongoing compliance of AI agents, making it easier to enforce ethical standards and regulatory requirements. In the event of a failure or harm caused by an AI agent, Trust Registries offer a clear point of reference for auditing and resolving accountability issues.

cheqd has developed a robust Trust Registry solution, enabling users to establish hierarchical chains of trust, with each registry entry being DID-resolvable for enhanced transparency and security. cheqd supports various Trust Registry Data Models, leveraging its versatile DID and DID-Linked Resource architecture.

## Get Started

If you are already aware of how cheqd's Decentralized Trust Chains (DTCs) are structured, you can get started immediately below. Otherwise, we suggest reading the rest of this page:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Build an AI Agent Trust Registry</strong></mark></td><td>Establish a comprehensive ecosystem for trusted AI Agents, organisations and governance authorities.</td><td><a href="build-trust-registry.md">build-trust-registry.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Verifiable Credentials to AI Agent</strong></mark></td><td>Using your Trust Registry, issue your AI Agent a credential fully referencing the Trust Chain back to a Root DID.</td><td><a href="issue.md">issue.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Validate AI Agent Trust Chain</strong></mark></td><td>Use our Validation APIs to enable your AI Agent to autonomously prove that it is trusted and accredited.</td><td><a href="validate.md">validate.md</a></td></tr></tbody></table>





## Establishing a Trust Hierarchy <a href="#hierarchy-examples" id="hierarchy-examples"></a>

cheqd's Trust Registry model is predicated on the notion of a trust hierarchy, which is conceptually very similar to traditional Public Key Infrastructure (PKI). Specifically, the model relies on a **Root of Trust** from which trusted relationships can be established.

In our model for AI Agents, each organisation in the trust hierarchy is able to issue Verifiable Accreditations to other entities, conveying a set of permissions or scopes that determine what the recipient entity is permitted to do.&#x20;

The following diagram shows an example of how an AI Agent Creator can accredit two AI Agents lower in the hierarchy:

<figure><img src="../.gitbook/assets/AI Agent Trust Registries.jpg" alt=""><figcaption></figcaption></figure>

Through this type of relationship, an AI Agent can prove that it is accredited by an AI Agent Creator through presenting the Verifiable Accreditation, which is stored on the cheqd blockchain.

Similarly, an AI Agent creator can prove that it is also trustworthy, demonstrating that it is a real actor rather than a fraudulent actor. In the diagram below, a Governance Authority (such as an accreditation body for AI Agent Creators) can accredit AI Agent Creators directly.

<figure><img src="../.gitbook/assets/AI Agent Trust Registries (Governance Authority).jpg" alt=""><figcaption></figcaption></figure>

Therefore, relying parties can query the accreditations of AI Agents all the way back to a Root of Trust.

## Verifiable Accreditations

Verifiable Accreditations may be issued between two different entities. Each Accreditation must include the DID of the issuer and the DID of the subject, as well as a set of claims or permissions.

An example of a Verifiable Accreditation can be found below:

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/vc/status-list/2021/v1"
  ],
  "id": "urn:uuid:123e4567-e89b-12d3-a456-426614174000",
  "type": ["VerifiableCredential", "VerifiableAuthorisationForTrustChain", "AIAgentAuthorization"],
  "issuer": "did:cheqd:testnet:12345678-90ab-cdef-1234-567890abcdef",
  "issuanceDate": "2025-03-28T12:00:00Z",
  "credentialSubject": {
    "accreditedFor": [
      {
        "schemaId": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e?resourceName=AIAgentAuthorization&resourceType=JSONSchemaValidator2020",
        "types": [
          "VerifiableCredential",
          "VerifiableAccreditation",
          "VerifiableAttestation",
          "VerifiableAccreditationToAccredit",
          "AIAgentAuthorization"
        ]
      }
    ],
    "id": "did:cheqd:testnet:abcdef12-3456-7890-abcd-ef1234567890"
  },
  "termsOfUse": {
    "type": "TrustFrameworkPolicy",
    "trustFramework": "AI Agent Governance Framework",
    "trustFrameworkId": "https://example.com/ai-agent-governance-framework"
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2025-03-28T12:00:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:cheqd:testnet:12345678-90ab-cdef-1234-567890abcdef#keys-1",
    "signature": "base64-encoded-signature"
  }
}

```

This accreditation gives permissions for the subject to issue accreditations of a particular schema, AI Agent Authorization. This is a Root Accreditation which references a Governance Framework, under which AI Agent Creators may be accredited or authorised.

Following this accreditation, the AI Agent Creator can issue the AI Agent a Verifiable Attestation conforming to the schema, for example:

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://example.com/ai-agent-context"
  ],
  "id": "urn:uuid:123e4567-e89b-12d3-a456-426614174000",
  "type": ["VerifiableCredential", "AIAgentAccreditation"],
  "issuer": "did:cheqd:testnet:b003df6f-ec8e-48dd-9a2b-7011c5cf0a5e",
  "issuanceDate": "2025-03-28T12:00:00Z",
  "credentialSubject": {
    "id": "did:cheqd:testnet:41d27ee4-437c-4a0e-84e0-783001a621d1",
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
    "evaluationMetrics": ["BLEU-4", "ROUGE-L", "F1 Score: 92.5%"],
    "certificationAuthority": "AI Ethics Board",
    "validUntil": "2026-03-28T12:00:00Z"
  },
  "termsOfUse": {
    "type": "VerifiableAttestation",
    "parentAccreditation": "did:cheqd:testnet:abcdef12-3456-7890-abcd-ef1234567890?resourceName=AIAgentAuthorization&resourceType=VerifiableAuthorisationForTrustChain",
    "rootAuthorisation": "did:cheqd:testnet:abcdef12-3456-7890-abcd-ef1234567890?resourceName=AIAgentAuthorization&resourceType=VerifiableAuthorisationForTrustChain"
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2025-03-28T12:05:00Z",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:cheqd:testnet:issuer123#keys-1",
    "signature": "z5MkG2jD3...abcdef12345"
  }
}

```

The Credential above lists some of the core metadata and information that defines the AI Agent. The AI Agent may be prompted to return this credential, and a relying party will be able to trust that the AI is trustworthy, and that it comes from a legitimate/accredited Creator.
