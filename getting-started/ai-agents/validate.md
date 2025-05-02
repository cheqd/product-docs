---
description: Verify and validate your AI Agent's trust with cheqd's MCP server and TRAIN.
---

# 🤝 Validate AI Agent Trust Chain

> ⚠️ **Before you begin...**
>
> Make sure you have configured the [MCP Client as per the setup instructions](trust-registry/setup-mcp/#getting-started).

To validate your AI Agent, you can simply use the `whois` prompt provided by the Cheqd MCP Server.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-01 at 16.44.58.png" alt=""><figcaption></figcaption></figure>

Then Click "Send" button without writing any other prompt:

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-01 at 16.46.15.png" alt=""><figcaption></figcaption></figure>

You can also use `/<mcp-server-name>:whois` if using a CLI client:

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-01 at 16.49.56.png" alt=""><figcaption></figcaption></figure>

The response should be similar to below. Claude/Client should use `list-credentials` to get the latest credentials from Agent's wallet, and then use `verify-trust-registry` tool to verify the credential.

<figure><img src="../../.gitbook/assets/Screenshot 2025-05-01 at 16.53.34.png" alt=""><figcaption></figcaption></figure>

Then present a summary of the credential:

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Learn more about what's happening under the hood:

Under the hood, the MCP Server is using our trust registry validation engine called TRAIN to recursively verify the issuer of the credential, up to a root of trust.&#x20;

{% hint style="success" %}
If you do not want to use MCP, you can build and validate cheqd Trust Registries in any alternative application using our more generalised tutorials.
{% endhint %}

Read more about our Decentralized Trust Chain (DTC) approach and how TRAIN works below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Decentralized Trust Chains (DTCs)</strong></mark></td><td>Build using our Trust Registry solution using DIDs and DID-Linked Resources based on the EBSI Trust Chain model.</td><td></td><td><a href="../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td><td><a href="../../studio/trust-registries/dtc/">dtc</a></td></tr><tr><td><mark style="color:blue;"><strong>Get Started With TRAIN</strong></mark></td><td>Deploy TRAIN and start validating trust chains with DNS-anchored roots and cryptographic accreditations.</td><td></td><td><a href="../../.gitbook/assets/fraunhofer.png">fraunhofer.png</a></td><td><a href="../../studio/trust-registries/train/">train</a></td></tr></tbody></table>
