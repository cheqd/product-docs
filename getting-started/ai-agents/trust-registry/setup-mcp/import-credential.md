---
description: Accept a JWT Credential into the AI Agent's wallet.
---

# Import Credential to AI Agent

As per [our instructions](../setup/issue-credential.md) on issuing a verifiable credential to your AI Agent, you can now use the Agent's DID as the `subjectDID` to create an accreditation for the agent using Cheqd Studio. The response from [Step 4](../setup/issue-credential.md#step-4-issue-verifiable-credential-to-ai-agent) will include a JWT token in the `proof` section.

Copy the JWT token and ask Claude Desktop (or any your choice of MCP Client) to accept the credential as follows:

> Can you accept the credential from this JWT token "eyJhbGciOiJFZ....."

Claude/Client should use the `import-credential` tool to add this credential to it's wallet as shown below:

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-01 at 16.39.07.png" alt=""><figcaption></figcaption></figure>

## What is happening under the hood?

In the background, Claude is communicating with our MCP Server which relays a request to one of our SDKs, [Credo](../../../../sdk/credo/). Using Credo, the AI Agent imports a Credential into the identity wallet associated with itself.
