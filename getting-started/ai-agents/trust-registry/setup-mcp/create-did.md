---
description: Use the cheqd MCP server to provision your AI Agent its own DID.
---

# Create AI Agent DID

> ⚠️ **Before you begin...**
>
> Make sure you have configured the [MCP Client as per the setup instructions](./#getting-started).

After the MCP server is running and Claude Desktop (or any MCP Client) is connected, you can ask the agent to create a DID just by typing:

> Hello agent, can you create a DID on cheqd testnet?

{% hint style="success" %}
The agent may ask for human input to run the "create-did" MCP tool, please select "yes" or "allow".
{% endhint %}

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-01 at 16.26.33.png" alt=""><figcaption></figcaption></figure>

## What is happening under the hood?

In the background, Claude is communicating with our MCP Server which relays a request to one of our SDKs, [Credo](../../../../sdk/credo/). Using Credo, the AI Agent creates itself a DID that conforms to the [cheqd DID Method](../../../../architecture/adr-list/adr-001-cheqd-did-method.md).
