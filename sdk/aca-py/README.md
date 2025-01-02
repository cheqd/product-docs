---
description: ACA-Py Plugin with cheqd support
---

# 🍊 ACA-Py

Aries Cloud Agent Python (ACA-Py) serves as a foundational tool for developing Verifiable Credential (VC) ecosystems. It functions at the second and third layers of the [Trust Over IP framework](https://trustoverip.org/wp-content/uploads/2020/05/toip_050520_primer.pdf) and supports multiple credential formats and protocols. As part of the [Open Wallet Foundation](https://openwallet.foundation/), ACA-Py's capabilities can be expanded with runtime-loaded plug-ins. Explore a diverse range of plug-ins in the [ACA-Py Plugins repository](https://plugins.aca-py.org/latest/).

ACA-Py now includes support for the **Cheqd DID** method, facilitating integration with Cheqd's decentralized identity ecosystem. Developers can utilize Cheqd for DID creation, resolution, and operations like issuing and verifying AnonCreds credentials. This offers an easy transition from the decommissioned Sovrin network to a modern, scalable alternative without disrupting existing workflows. Cheqd is fully supported in ACA-Py for issuing Verifiable Credentials, creating Credential Schemas, and Credential Definitions.

AnonCreds using cheqd are facilitated using the [cheqd AnonCreds Object Method](https://docs.cheqd.io/product/advanced/anoncreds).

## Get started <a href="#get-started" id="get-started"></a>

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Agent Setup</strong></mark></td><td>Setup the ACA-Py agent with cheqd plugin.</td><td></td><td><a href="./#setup-aca-py-agent">#setup-aca-py-agent</a></td></tr><tr><td><mark style="color:blue;"><strong>Create cheqd DIDs</strong></mark></td><td>Use the agent to create cheqd DIDs.</td><td></td><td><a href="decentralized-identifiers-dids/create-a-did.md">create-a-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue a Credential</strong></mark></td><td>Issue a verifiable credential using ACA-Py agent.</td><td></td><td><a href="verifiable-credentials-and-presentations/issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr></tbody></table>
