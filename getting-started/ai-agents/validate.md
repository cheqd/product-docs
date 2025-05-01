---
description: Verify and validate your AI Agent's trust with TRAIN
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
