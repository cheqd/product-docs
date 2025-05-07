---
description: >-
  Build and validate Decentralized Trust Chains, using cheqd Decentralized
  Identifiers (DIDs) and Verifiable Accreditations stored as DID-Linked
  Resources (DLRs).
---

# 🤝 Build Trust Registries

Trust Registries enable a Relying Party to determine the authenticity and authorization of a legal entity within a digital credential ecosystem. Trust Registries are crucial to establish for production environments, because they allow relying parties to make informed decisions on whether to trust the credentials they receive.

cheqd has **pioneered a industry-leading trust registry solution**, allowing users to **create hierarchical chains of trust, with each trust registry entry being DID-Resolvable**.

## cheqd’s Trust Registry Model

cheqd offers an **industry-leading, decentralized trust registry solution**, allowing issuers to be accredited through **hierarchical chains of trust called Decentralized Trust Chains (DTCs)**. Each entry in the registry is:

* ✅ **DID-resolvable**
* 📜 **Cryptographically signed and verifiable**
* 🌍 **Publicly discoverable using DID-Linked Resources**
* 🔗 **Linked to governance frameworks**

## Learn about cheqd Trust Registries

cheqd enables you to build **Decentralized Trust Chains** — a powerful model for structuring trust in credential ecosystems without relying on centralized authorities or static whitelists.

Using cheqd’s flexible **DID** and **DID-Linked Resource** architecture, trust is established through a chain of cryptographically verifiable credentials:

* **Root Authorisations** define the governance framework and root of trust
* **Verifiable Accreditations** delegate authority to trusted organisations
* 🏷**Verifiable Attestations** **(Credentials)** are issued by accredited entities to end users

Each link in the chain is publicly resolvable, tamper-evident, and policy-bound — forming a complete lineage of trust from issuer to root authority.

Before you begin building, we recommend familiarising yourself with how Decentralized Trust Chains work and the role of each credential type:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Decentralized Trust Chains (DTCs)</strong></mark></td><td>Build using our Trust Registry solution using DIDs and DID-Linked Resources based on the EBSI Trust Chain model.</td><td></td><td><a href="../../.gitbook/assets/decentralized trust chains.png">decentralized trust chains.png</a></td><td><a href="dtc/">dtc</a></td></tr></tbody></table>

## Get started

Use **cheqd Studio APIs** to define, issue, and publish trust registry entries:

* Create and manage **Root Authorisations**, **Accreditations**, and **Attestations**
* Resolve trust chains in real time using standard DID resolution
* Anchor trust registries on-chain while keeping business logic off-chain

For verification, use TRAIN to validate the trust registry to determine whether an issuer DID is accredited, and what they are accredited for — by traversing the full trust chain to its root.&#x20;

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Set up Trust Chain</strong></mark></td><td>Design and build a trust chain for establishing a trust hierarchy in your ecosystem.</td><td><a href="set-up/">set-up</a></td><td><a href="../../.gitbook/assets/Set Up Trust Chain.png">Set Up Trust Chain.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Get started with TRAIN</strong></mark></td><td>Deploy TRAIN and start validating trust chains with DNS-anchored roots and cryptographic accreditations.</td><td><a href="train/">train</a></td><td><a href="../../.gitbook/assets/fraunhofer.png">fraunhofer.png</a></td></tr><tr><td><mark style="color:blue;"><strong>Use Trust Registries for AI Agents</strong></mark></td><td>Design, build and validate trust registries for AI Agents using cheqd's APIs and our MCP Server.</td><td><a href="../../getting-started/ai-agents/">ai-agents</a></td><td><a href="../../.gitbook/assets/Setup AI Agent Trust Registry.png">Setup AI Agent Trust Registry.png</a></td></tr></tbody></table>
