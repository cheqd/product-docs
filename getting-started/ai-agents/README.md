---
description: Design, Build and Validate Trust Registries for AI Agents on cheqd.
---

# ☑️ Use Trust Registries for AI Agents

## Get Started

cheqd Trust Registries can be designed and built by companies that create AI agents, and equally, validated against by users of AI agents. Choose what type of user you are below to get started:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><span data-gb-custom-inline data-tag="emoji" data-code="1f590">🖐️</span> <mark style="color:blue;"><strong>I want to set up a Trust Registry for my AI Agent</strong></mark></td><td>Design, build and configure your AI Agent trust registry with cheqd Studio and our MCP server. </td><td><a href="trust-registry/">trust-registry</a></td><td><a href="../../.gitbook/assets/Trust Registry Builder.png">Trust Registry Builder.png</a></td></tr><tr><td><span data-gb-custom-inline data-tag="emoji" data-code="2611">☑️</span> <mark style="color:blue;"><strong>I want to verify the trust in AI Agents I am using</strong></mark></td><td>Use our trust registry validation prompts to get AI agents to identify themselves and demonstrate their trustworthiness against a trust registry.</td><td><a href="validate.md">validate.md</a></td><td><a href="../../.gitbook/assets/AI Agent User.png">AI Agent User.png</a></td></tr></tbody></table>

## What are Trust Registries for AI Agents? <a href="#hierarchy-examples" id="hierarchy-examples"></a>

Trust Registries play a critical role in establishing trust for AI Agents. Acting as authoritative databases, they can **verify the AI Agent is accredited and authorised** to carry out certain actions, ensuring transparency and accountability.

Here’s how they support the AI ecosystem:

* **Accreditation of AI Agents**: Trust Registries maintain verified records of AI agents, ensuring that they are accredited by trusted organisations or ecosystems. Through accreditations, these trust registries validate that AI agents are legitimate and scope the permissions under which the AI agent can operate within.
* **Transparency**: Trust Registries enable users and organisations to query and verify the origins and accreditations of AI systems they engage with. By providing a publicly accessible record of trusted AI agents, Trust Registries empower stakeholders to assess the credibility and history of an AI system before utilising it. This enhances confidence in the system, especially when the AI’s decisions impact sensitive areas like personal data or legal outcomes.
* **Governance**: Trust Registries also serve as a governance tool, ensuring that AI developers and platforms are held accountable for their actions. By maintaining a registry of accredited AI systems, these registries can track the ongoing compliance of AI agents, making it easier to enforce ethical standards and regulatory requirements. In the event of a failure or harm caused by an AI agent, Trust Registries offer a clear point of reference for auditing and resolving accountability issues.

cheqd has developed a robust Trust Registry solution, enabling users to establish hierarchical chains of trust, with each registry entry being DID-resolvable for enhanced transparency and security. cheqd supports various Trust Registry Data Models, leveraging its versatile DID and DID-Linked Resource architecture.

## Establishing a Trust Hierarchy

cheqd's Trust Registry model is predicated on the notion of a trust hierarchy, which is conceptually very similar to traditional Public Key Infrastructure (PKI). Specifically, the model relies on a **Root of Trust** from which trusted relationships can be established.

In our model for AI Agents, each organisation in the trust hierarchy is able to issue Verifiable Accreditations to other entities, conveying a set of permissions or scopes that determine what the recipient entity is permitted to do.&#x20;

The following diagram shows an example of how an AI Agent Creator can accredit two AI Agents lower in the hierarchy:

<figure><img src="../../.gitbook/assets/AI Agent Trust Registries.jpg" alt=""><figcaption><p>Root DID (rTAO) accrediting or authorising sub-DIDs (TAOs)</p></figcaption></figure>

Through this type of relationship, an AI Agent can prove that it is accredited by an AI Agent Creator through presenting the Verifiable Accreditation, which is stored on the cheqd blockchain.

Similarly, an AI Agent creator can prove that it is also trustworthy, demonstrating that it is a real actor rather than a fraudulent actor. In the diagram below, a Governance Authority (such as an accreditation body for AI Agent Creators) can accredit AI Agent Creators directly.

<figure><img src="../../.gitbook/assets/AI Agent Trust Registries (Governance Authority).jpg" alt=""><figcaption><p>Full tree diagram of root DID accrediting DIDs, that accredit AI Agents.</p></figcaption></figure>

Therefore, relying parties can query the accreditations of AI Agents all the way back to a Root of Trust.

## Learn more about cheqd's Trust Registry model

Read through some of our guides below to getting setup with trust registries on cheqd:

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Decentralized Trust Chains (DTCs)</strong></mark></td><td>Take a deep dive into the architecture and model for cheqd's trust registries.</td><td><a href="../../studio/trust-registries/dtc/">dtc</a></td></tr><tr><td><mark style="color:blue;"><strong>Set up Trust Chain</strong></mark></td><td>Set up a generic trust chain using our cheqd Studio APIs. </td><td><a href="../../studio/trust-registries/set-up/">set-up</a></td></tr><tr><td><mark style="color:blue;"><strong>Get Started with TRAIN</strong></mark></td><td>Set up and start using our trust registry valdation engine called TRAIN.</td><td><a href="../../studio/trust-registries/train/">train</a></td></tr></tbody></table>
