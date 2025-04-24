---
description: >-
  Aries Cloud Agent Python (ACA-Py) Plugin with cheqd support for DIDs,
  DID-Linked Resources (DLRs) and Credentials.
cover: ../../.gitbook/assets/ACA-Py.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# 🍊 ACA-Py

**Aries Cloud Agent Python (ACA-Py)** is a powerful and extensible agent framework for building Verifiable Credential (VC) ecosystems. Built on the **Aries RFCs** and aligned with the **Trust Over IP stack**, ACA-Py operates at **Layer 2 (Credential Exchange)** and **Layer 3 (Peer-to-Peer Communication)**.

As a project under the **Open Wallet Foundation**, ACA-Py supports dynamic extensions via runtime-loaded plugins, enabling flexible integration into enterprise and SSI platforms.

***

### DID and Credential Support

ACA-Py supports multiple **credential formats** and **DID methods**, including:

* **AnonCreds**
* **W3C Verifiable Credentials (JSON-LD)**
* **DIDComm v1 & v2**
* **DID method plugins** like `did:peer`, `did:indy`, and `did:cheqd`

***

### ACA-Py + cheqd: Modern Verifiable Credential Issuance

ACA-Py now includes full support for the **`did:cheqd`** method, enabling seamless integration with the **cheqd decentralized identity network**. This allows developers to:

* Create and resolve `did:cheqd` identifiers
* Publish **Credential Schemas** and **Credential Definitions** to cheqd
* Issue and verify **AnonCreds** credentials using cheqd as the backing ledger
* Replace legacy `did:sov`/Sovrin usage with a modern, scalable alternative

> 💡 AnonCreds on cheqd are enabled via the **cheqd AnonCreds Object Method**, maintaining compatibility with ACA-Py's credential exchange workflows.

***

### Universal Registrar Integration

The **cheqd plugin for ACA-Py** supports dynamic DID registration via the **Universal Registrar**. This provides a streamlined interface for managing multiple DID methods.

#### Benefits of Using Universal Registrar:

* **Unified DID Support**\
  Register multiple DID methods (e.g., `did:cheqd`, `did:key`, `did:web`) through a single unified API.
* **Streamlined Setup**\
  Eliminate manual configuration—DIDs are created dynamically at runtime by calling the relevant driver.
* **Interoperable by Design**\
  Easily switch or support multiple DID methods in the same ACA-Py deployment.

## Get started <a href="#get-started" id="get-started"></a>

Get setup with your ACA-Py agent and begin using cheqd's functionality below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Agent Setup</strong></mark></td><td>Setup the ACA-Py agent with cheqd plugin.</td><td></td><td><a href="setup-aca-py-agent.md">setup-aca-py-agent.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create cheqd DIDs</strong></mark></td><td>Use the agent to create cheqd DIDs.</td><td></td><td><a href="decentralized-identifiers-dids/create-a-did.md">create-a-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Create DID-Linked Resources</strong></mark></td><td>Create a DID-Linked Resource (DLR) linked to a cheqd DID.</td><td></td><td><a href="did-linked-resources/">did-linked-resources</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Credential</strong></mark></td><td>Issue a Verifiable Credential using ACA-Py agent with <code>did:cheqd</code>.</td><td></td><td><a href="verifiable-credentials-and-presentations/issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr></tbody></table>
