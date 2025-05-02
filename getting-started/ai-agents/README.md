---
description: Design, Build and Validate Trust Registries for AI Agents on cheqd.
---

# ☑️ Use Trust Registries for AI Agents

## Get Started

cheqd Trust Registries can be designed and built by companies that create AI agents, and verified by users of AI agents. Select your user type below to begin:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><span data-gb-custom-inline data-tag="emoji" data-code="1f590">🖐️</span> <mark style="color:blue;"><strong>I want to set up a Trust Registry for my AI Agent</strong></mark></td><td>Design, build and configure your AI Agent trust registry with cheqd Studio and our MCP server. </td><td><a href="trust-registry/">trust-registry</a></td><td><a href="../../.gitbook/assets/Trust Registry Builder.png">Trust Registry Builder.png</a></td></tr><tr><td><span data-gb-custom-inline data-tag="emoji" data-code="2705">✅</span> <mark style="color:blue;"><strong>I want to verify the trust in AI Agents I am using</strong></mark></td><td>Use our trust registry validation prompts to get AI agents to identify themselves and demonstrate their trustworthiness against a trust registry.</td><td><a href="validate.md">validate.md</a></td><td><a href="../../.gitbook/assets/AI Agent User.png">AI Agent User.png</a></td></tr></tbody></table>

## What are Trust Registries for AI Agents? <a href="#hierarchy-examples" id="hierarchy-examples"></a>

Trust Registries play a critical role in establishing trust for AI Agents. Acting as authoritative databases, they can **verify the AI Agent is accredited and authorised** to carry out certain actions, ensuring transparency and accountability.

Here’s how they support the AI ecosystem:

* **Accreditation of AI Agents**: Trust Registries maintain verified records of AI agents, ensuring that they are accredited by trusted organisations or ecosystems. Through accreditations, these trust registries validate that AI agents are legitimate and scope the permissions under which the AI agent can operate within.
* **Transparency**: Trust Registries enable users and organisations to query and verify the origins and accreditations of AI systems they engage with. By providing a publicly accessible record of trusted AI agents, Trust Registries empower stakeholders to assess the credibility and history of an AI system before utilising it. This enhances confidence in the system, especially when the AI’s decisions impact sensitive areas like personal data or legal outcomes.
* **Governance**: Trust Registries also serve as a governance tool, ensuring that AI developers and platforms are held accountable for their actions. By maintaining a registry of accredited AI systems, these registries can track the ongoing compliance of AI agents, making it easier to enforce ethical standards and regulatory requirements. In the event of a failure or harm caused by an AI agent, Trust Registries offer a clear point of reference for auditing and resolving accountability issues.

cheqd has developed a robust Trust Registry solution, enabling users to establish hierarchical chains of trust, with each registry entry being DID-resolvable for enhanced transparency and security.

## Capabilities of cheqd's Trust Registries for AI Agents

cheqd’s Agentic Trust combine and build upon tried and tested capabilities, already deployed at scale:

* Integrations with popular AI/ML tooling, such as MCP servers for easing how AI agents can interface with decentralised identity.
* Work with our SaaS (cheqd Studio) or Software Development Kits (SDKs) depending on your desired level of control versus simplicity.
* SDKs in multiple software languages to suit preferences / skillsets.
* Industry-leading whilst interoperable capabilities. e.g. DIDs, DID-linked resources, and Trust Registries.

<figure><img src="../../.gitbook/assets/cheqd AI Trust Registry Capabilities.png" alt=""><figcaption></figcaption></figure>

## Components of cheqd's AI Trust Registry Solution

Authorities can author trust registries and issue identifiers to agents using:&#x20;

* cheqd studio (SaaS APIs) for easy and quick integration with no deployment requirements
* SDKs for more control and tighter integration

Agent frameworks, deployments or end-users can use:

* Model Context Protocol (MCP) to surface responses directly to users via the agent, including a /who\_is command which has the agent verify itself.
* Hosted TRAIN APIs to verify Agent accreditations.
* SDKs to hold & proof credentials, including verifying signatures.

<figure><img src="../../.gitbook/assets/cheqd AI Trust Registry Components.png" alt=""><figcaption></figcaption></figure>

## Establishing a Trust Hierarchy

cheqd's Trust Registry model is predicated on the notion of a trust hierarchy, which is conceptually very similar to traditional Public Key Infrastructure (PKI). Specifically, the model relies on a **Root of Trust** from which trusted relationships can be established.

In our model for AI Agents, each organisation in the trust hierarchy is able to issue Verifiable Accreditations to other entities, conveying a set of permissions or scopes that determine what the recipient entity is permitted to do.&#x20;

The following diagram shows an example of how an AI Agent Creator can accredit two AI Agents lower in the hierarchy:

<figure><img src="../../.gitbook/assets/AI Agent Trust Registries.jpg" alt=""><figcaption><p>Root DID (rTAO) accrediting or authorising sub-DIDs (TAOs)</p></figcaption></figure>

Through this type of relationship, an AI Agent can prove that it is accredited by an AI Agent Creator through presenting the Verifiable Accreditation, which is stored on the cheqd blockchain.

Similarly, an AI Agent creator can prove that it is also trustworthy, demonstrating that it is a real actor rather than a fraudulent actor. In the diagram below, a Governance Authority (such as an accreditation body for AI Agent Creators) can accredit AI Agent Creators directly.

<figure><img src="../../.gitbook/assets/AI Agent Trust Registries (Governance Authority).jpg" alt=""><figcaption><p>Full tree diagram of root DID accrediting DIDs, that accredit AI Agents.</p></figcaption></figure>

Therefore, relying parties can query the accreditations of AI Agents all the way back to a Root of Trust.

## Use cases for AI Agent Trust registries

Below are a series of use cases to which this solution architecture can be applied:

| Use-case                         | Description                                                                                                                 | Governance authority             | (Governance authority issued) Verifiable Accreditation | Organisation     | (Organisation issued) Verifiable Accreditation |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ------------------------------------------------------ | ---------------- | ---------------------------------------------- |
| **Ownership / representation**   | Verification of which agents are approved to represent which company for a given scope.                                     | Group company                    | Company relationship                                   | Subsidiary       | Scope of representation                        |
| **Agent Bill of Materials**      | Verifiable proof of the “supply chain” of an AI Agent, including who, how and when it was developed, trained, and deployed. | Framework                        | Agent Bill of Materials (e.g. model, corpus, used)     | Not applicable   | Not applicable                                 |
| **Regulatory compliance**        | Verifiable compliance with a given regulatory scheme for a given period.                                                    | Government (e.g. EU)             | Authority to audit against EU AI Act                   | Auditor          | Agent Compliance Certificate                   |
| **Industry standard compliance** | Verifiable compliance with an industry-body administered scheme.                                                            | Industry body (e.g. DAIAA, SAIA) | Agent Compliance Certificate (issued to Agent)         | Not applicable   | Not applicable                                 |
| **Security audit**               | Proof of a suitable and reputable security audit.                                                                           | Security company                 | Agent Compliance Certificate (issued to Agent)         | Not applicable   | Not applicable                                 |
| **Generic trust check**          | The ability to state that a company or AI Agent trusts another for a given context.                                         | Any organisation                 | “I trust this organisation”                            | Any organisation | “I trust this agent”                           |

## Learn more about cheqd's Trust Registry model

Read through some of our guides below to getting setup with trust registries on cheqd:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Decentralized Trust Chains (DTCs)</strong></mark></td><td>Take a deep dive into the architecture and model for cheqd's trust registries.</td><td><a href="../../studio/trust-registries/dtc/">dtc</a></td></tr><tr><td><mark style="color:blue;"><strong>Set up Trust Chain</strong></mark></td><td>Set up a generic trust chain using our cheqd Studio APIs. </td><td><a href="../../studio/trust-registries/set-up/">set-up</a></td></tr><tr><td><mark style="color:blue;"><strong>Get Started with TRAIN</strong></mark></td><td>Set up and start using our trust registry valdation engine called TRAIN.</td><td><a href="../../studio/trust-registries/train/">train</a></td></tr></tbody></table>
