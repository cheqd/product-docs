---
description: >-
  DID-anchored identity, delegation and per-request proof for agentic
  protocols — A2A and AP2 — built on did:cheqd.
---

# 🤖 Agent Identity (A2A / AP2)

> **Beta — not yet externally reviewed.** The packages below are published and usable, but have not had an independent security review, and the API may still change. See each package's README for current status before depending on it in production.

[`cheqd/agent-toolkit`](https://github.com/cheqd/agent-toolkit) binds `did:cheqd` identity directly into two agentic protocols, so that a message or a payment mandate between agents carries a verifiable, DID-anchored proof of who sent it — not just who an agent claims to be in its profile.

This is a different layer to the [Trust Registries for AI Agents](../getting-started/ai-agents/) above: a trust registry proves an agent is *accredited*; Agent Identity proves that *this specific message, right now* came from the key that accreditation points to.

## Key Features

* **A2A trust binding** — a client interceptor and server executor gate for the [A2A protocol](https://github.com/a2aproject/A2A), adding a per-request, holder-of-key proof (`org.kya-os/proof.v1`) over the request, plus an RFC 9421 HTTP Message Signature sibling.
* **AP2 binding** — DID-anchored `cnf` key continuity and mandate-chain verification for the [AP2 protocol](https://github.com/google-agentic-commerce/AP2)'s payment mandates, as a compact SD-JWT-VC + Key Binding JWT presentation.
* **DID-anchored throughout** — agent identity, delegation and revocation all resolve through `did:cheqd`, including reciprocal `did:web` ↔ `did:cheqd` linkage and DID-Linked Resource-based credential status.
* **Cross-language conformance** — the TypeScript and Python implementations are verified interoperable with each other on every push, not just internally consistent.
* **Available in TypeScript and Python**, package-for-package.

## Ideal for Developers Who

* Are building agents on the A2A or AP2 protocols and need the other side to be able to verify *which DID* sent a given message or mandate, not just trust a self-asserted identity.
* Want DID-anchored proof-of-possession on individual protocol messages, as a complement to (not a replacement for) a [Trust Registry](../getting-started/ai-agents/) that governs what an accredited agent is allowed to do.
* Need the same guarantees from both a TypeScript and a Python agent in the same system.

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>TypeScript (npm)</strong></mark></td><td>Install the core identity package plus the A2A and/or AP2 binding you need, tagged <code>beta</code>.</td><td><a href="https://www.npmjs.com/package/@cheqd/agent-identity">https://www.npmjs.com/package/@cheqd/agent-identity</a></td></tr><tr><td><mark style="color:blue;"><strong>Python (PyPI)</strong></mark></td><td>Coming soon — the Python port is feature-complete and cross-language conformant with the TypeScript packages, published here once available.</td><td><a href="https://github.com/cheqd/agent-toolkit/tree/main/python/packages">https://github.com/cheqd/agent-toolkit/tree/main/python/packages</a></td></tr><tr><td><mark style="color:blue;"><strong>Design decisions</strong></mark></td><td>The full design-decision log and research behind this programme's choices.</td><td><a href="https://github.com/cheqd/agent-toolkit/blob/main/docs/design-decisions.md">https://github.com/cheqd/agent-toolkit/blob/main/docs/design-decisions.md</a></td></tr></tbody></table>

### Install (TypeScript)

```bash
npm install @cheqd/agent-identity@beta
npm install @cheqd/agent-identity-a2a@beta   # for A2A
npm install @cheqd/agent-identity-ap2@beta   # for AP2
```

### Install (Python)

```bash
pip install cheqd-agent-identity
pip install cheqd-agent-identity-a2a   # for A2A
pip install cheqd-agent-identity-ap2   # for AP2
```

> Python packages are not yet published — this is the install command to use once they are.

## Architecture

The core package (`@cheqd/agent-identity` / `cheqd-agent-identity`) handles DID resolution, reciprocal `did:web` ↔ `did:cheqd` linkage, DID-Linked Resource policy, credential status and key resolution. The A2A and AP2 bindings both depend on the core package and not on each other, so an AP2 integration never needs to install the A2A SDK, and vice versa.

## Known gaps

As a beta, the following are tracked as open rather than hidden:

* KMS/HSM-backed signing has a seam (an external `signer` callback can be supplied instead of a raw private key) but is not yet proven against a real KMS or HSM.
* AP2 multi-hop re-delegation is out of scope — single-delegation-hop mandates only.
* No gRPC transport or streaming support yet.
* No external architectural review yet — required before a stable/GA release, not before this beta.

See the [design-decision log](https://github.com/cheqd/agent-toolkit/blob/main/docs/design-decisions.md) and [threat model](https://github.com/cheqd/agent-toolkit/blob/main/docs/security/threat-model.md) for the full, current list.
