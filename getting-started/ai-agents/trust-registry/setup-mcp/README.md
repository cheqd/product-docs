---
description: >-
  Setup and Configure your MCP Server to begin using cheqd's identity
  functionality with your AI Agent.
---

# Setup and Configure MCP Server

> This is an **advanced** guide for those who want to integrate AI Agents with the Cheqd Network to create DIDs, Issue Verifiable Credentials and store Verifiable Credentials.

## Overview

The **Cheqd MCP Server** is a specialised implementation of the [Model Context Protocol](https://modelcontextprotocol.io/introduction) (MCP) that integrates with the **cheqd** network to provide secure decentralised identity management capabilities for AI applications. Built on the powerful combination of MCP's standardised context protocol and **cheqd**'s robust identity infrastructure, this server enables AI agents to perform sophisticated identity operations while maintaining security, privacy, and verifiability.

Our MCP server is designed as a modular toolkit that can be easily integrated with existing AI workflows, particularly through Claude Desktop, Cursor, and other MCP-compatible client applications.&#x20;

### Benefits of using MCP with cheqd

* **Enhanced AI Trust and Verification**: Enable AI agents to create, resolve, and verify decentralised identities, establishing a foundation of trust for AI interactions.
* **Verifiable Credential Management**: Simplify the issuance, verification, and management of verifiable credentials through standardised tools.
* **Decentralised Identity Operations**: Leverage cheqd's blockchain infrastructure for creating and managing DIDs with cryptographic security.
* **Standardised AI Context**: Utilise the Model Context Protocol to provide structured and verifiable context for AI interactions.
* **Human-in-the-loop Security**: MCP's approval-based tool execution model ensures humans maintain oversight of sensitive identity operations.
* **Interoperable Integration**: Seamlessly connect with existing identity ecosystems through the cheqd network and Credo Toolkit.

### Integration with Credo Toolkit

The server includes deep integration with the Credo Toolkit ([`@cheqd/mcp-toolkit-credo`](https://www.npmjs.com/package/@cheqd/mcp-toolkit-credo)), which provides a comprehensive set of tools for interacting with the cheqd network through the OpenWalletFoundation's Credo-TS framework. This integration enables:

* **DID Management**: Create, update, resolve, and deactivate DIDs on the cheqd network.
* **Resource Operations**: Create and resolve DID-linked resources.
* **Schema Management**: Define and retrieve credential schemas for standardised credential formats.
* **Connection Management**: Establish secure DIDComm connections between identity holders and verifiers.
* **Credential Issuance**: Generate and issue verifiable credentials with cryptographic proofs.
* **Verification Protocols**: Implement zero-knowledge proof verification for enhanced privacy.

The Credo Toolkit abstracts the complexity of these operations, presenting them as simple, well-documented MCP tools that can be invoked by AI agents with appropriate permissions. By leveraging the Credo Toolkit integration, the MCP server provides comprehensive tools for managing the full lifecycle of decentralised identifiers (DIDs) and verifiable credentials on the cheqd network.

### Target Use Cases and Scenarios

The Cheqd MCP Server is particularly well-suited for scenarios requiring trusted AI interactions with verifiable identity information:

* **AI-Assisted Identity Verification**: Enable AI agents to verify user identities through cryptographically secured credentials.
* **Credential Issuance Workflows**: Streamline the creation and issuance of verifiable credentials through AI-guided processes.
* **Trusted Data Exchange**: Facilitate secure sharing of verified data between organisations using AI intermediaries.
* **Identity-Aware AI Systems**: Build AI applications that respect privacy and understand the provenance of identity information.
* **Cross-Domain Verification**: Enable verification of credentials across different systems and networks through the cheqd infrastructure.
* **Self-Sovereign Identity Management**: Support user control over identity information while enabling AI assistance for complex operations.

## Getting Started

### Prerequisites

* An MCP Compatible Desktop based **AI Agent**. We have tested with [Claude Desktop](https://claude.ai/download).
* **Working understanding of cheqd network**: Understanding of DID operations and verifiable credentials.
* **Node.js 20 or higher** (if running using npx).
* **Docker Desktop** (if running using docker). This is preferred when running the ACA-Py demo.

### Quick Start: Use the Remote MCP Server

The simplest way to get started with MCP and Trust Registry verification is to use our hosted remote MCP server.

#### Configuration

* Open your Claude Desktop configuration file:
  * **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
  * **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`
* Or, for Cursor `.cursor/mcp.json`&#x20;
* Add the following configuration:

```json
"mcpServers": {
    "cheqd-mcp": {
        "command": "npx",
        "args": [
            "mcp-remote",
            "https://remote-mcp.cheqd.io/sse",
            "--transport", "sse-only"
        ],
    }
}
```

* Save the file and restart Claude Desktop.
* You should now see the Trust Registry verification tools available in Claude Desktop/ Cursor.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-08 at 16.24.19.png" alt=""><figcaption></figcaption></figure>

{% embed url="https://youtu.be/sT6soNmMGIs" %}

### Alternative: Local Installation (For Developers)

If you prefer to run the MCP server locally, with more control on the configuration and environment variables, you can run locally using docker.

#### 1. Get the code

Get the `docker-compose.yaml` specially designed for Claude Desktop:&#x20;

```bash
# Clone the repository
git clone https://github.com/cheqd/mcp-toolkit.git
cd mcp-toolkit
```

#### 2. Create Configuration File

Update your local `env.example` file with the required environment variables:

{% code title="./env.example" %}
```env
TOOLS="credo"                                # Required tool configuration
PORT="5000"                                # The Port where the Remote MCP Server will run
CREDO_PORT="3000"                            # Port for the Credo agent
CREDO_NAME="faber"                        # Name of the Credo agent
CREDO_ENDPOINT="http://faber:3000"        # Public endpoint connections
CREDO_CHEQD_TESTNET_MNEMONIC="your-testnet-mnemonic" # Required for cheqd network access
TRAIN_ENDPOINT="https://dev-train.trust-scheme.de/tcr/v1/"    # The TRAIN endpoint for verification of trust registry 
```
{% endcode %}

Replace `your-testnet-mnemonic` with a valid mnemonic for the cheqd testnet. You can generate one using the cheqd CLI or get one from the cheqd faucet.

#### Server Configuration Options

<table><thead><tr><th width="291.85546875">Variable</th><th width="314.66796875">Description</th><th>Default</th></tr></thead><tbody><tr><td><code>TOOLS</code></td><td>Comma-separated list of tools to enable</td><td>"credo"</td></tr><tr><td><code>PORT</code></td><td>The port where remote MCP server will run</td><td>5000</td></tr></tbody></table>

#### Credo Tool Configuration Options

<table><thead><tr><th width="296.36328125">Variable</th><th width="314.140625">Description</th><th>Required</th></tr></thead><tbody><tr><td><code>CREDO_CHEQD_TESTNET_MNEMONIC</code></td><td>Mnemonic for cheqd testnet wallet</td><td>Yes</td></tr><tr><td><code>CREDO_PORT</code></td><td>Port for the Credo agent</td><td>No</td></tr><tr><td><code>CREDO_NAME</code></td><td>Name for the Credo agent</td><td>No</td></tr><tr><td><code>CREDO_ENDPOINT</code></td><td>Public endpoint for DIDComm connections</td><td>No</td></tr><tr><td><code>TRAIN_ENDPOINT</code></td><td>The endpoint for verifying trust registry</td><td>No</td></tr></tbody></table>

#### 3. Update Claude Config

Add the following configuration to your claude\_desktop\_config.json or .cursor/mcp.json

```json
{
  "mcpServers": {
    "cheqd": {
      "command": "docker",
      "args": [
        "compose",
        "-f",
        "/path/to/repo/mcp-toolkit/docker/docker-compose.yml",
        "run",
        "--rm",
        "-p", 
        "3000:3000",
        "--name",
        "faber",
        "-T",
        "mcp-server"
      ]
    }
  }
}
```

#### 4. Restart Claude Desktop

When successfully started, the MCP tools will be available.

<figure><img src="../../../../.gitbook/assets/Screenshot 2025-05-08 at 16.24.19 (1).png" alt=""><figcaption></figcaption></figure>

## Using the MCP with Trust Registry

Once configured, you can interact with the Trust Registry through Claude by:

1. Using the `verify-trust-registry` tool to verify credentials against the Trust Registry
2. Running a verification check for a cheqd DID or credential

For detailed usage examples, refer to our [Trust Registry Verification Guide](https://docs.cheqd.io/product/getting-started/ai-agents/validate).

## Next Steps

Now that you have the Cheqd MCP Server running, you can:

* Explore the available tools for DID management and credential operations
* Create your first verifiable credential using the provided tools
* Set up a complete identity verification workflow

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create AI Agent DID</strong></mark></td><td>Create a new did:cheqd DID for the AI Agent.</td><td><a href="create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Import AI Agent Credential</strong></mark></td><td>Import and provision a JWT Credential to your AI Agent</td><td><a href="import-credential.md">import-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Credentials</strong></mark></td><td>Issue Verifiable Credentials signed by cheqd DIDs using MCP Toolkit.</td><td><a href="advanced/issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Verify Credentials</strong></mark></td><td>Verify a Credential using MCP Toolkit.</td><td><a href="advanced/verify-credential.md">verify-credential.md</a></td></tr></tbody></table>

