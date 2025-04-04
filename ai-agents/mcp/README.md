# MCP Server Setup

> This is an **advanced** guide for those who want to integrate AI Agents with the Cheqd Network to create DIDs and Issue Verifiable Credentials.

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
* **Node.js 20 or higher** (if running using npx).
* **Docker Desktop** (if running using docker). This is preferred when running the ACA-Py demo.
* **Working understanding of cheqd network**: Understanding of DID operations and verifiable credentials

### Configuration

The Cheqd MCP Server is configurable through environment variables:

#### Server Configuration Options

<table><thead><tr><th width="291.85546875">Variable</th><th width="314.66796875">Description</th><th>Default</th></tr></thead><tbody><tr><td><code>TOOLS</code></td><td>Comma-separated list of tools to enable</td><td>"credo"</td></tr></tbody></table>

#### Credo Tool Configuration Options

<table><thead><tr><th width="296.36328125">Variable</th><th width="314.140625">Description</th><th>Required</th></tr></thead><tbody><tr><td><code>CREDO_CHEQD_TESTNET_MNEMONIC</code></td><td>Mnemonic for cheqd testnet wallet</td><td>Yes</td></tr><tr><td><code>CREDO_PORT</code></td><td>Port for the Credo agent</td><td>No</td></tr><tr><td><code>CREDO_NAME</code></td><td>Name for the Credo agent</td><td>No</td></tr><tr><td><code>CREDO_ENDPOINT</code></td><td>Public endpoint for DIDComm connections</td><td>No</td></tr></tbody></table>

{% embed url="https://youtu.be/sT6soNmMGIs" %}

### Setup

#### 1. Get the code

Get the `docker-compose.yaml` specially designed for Claude:&#x20;

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
CREDO_PORT="3000"                            # Port for the Credo agent
CREDO_NAME="faber"                        # Name of the Credo agent
CREDO_ENDPOINT="http://faber:3000"        # Public endpoint connections
CREDO_CHEQD_TESTNET_MNEMONIC="your-testnet-mnemonic" # Required for cheqd network access
```
{% endcode %}

Replace `your-testnet-mnemonic` with a valid mnemonic for the cheqd testnet. You can generate one using the cheqd CLI or get one from the cheqd faucet.

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

When successfully started, the number of tools available to Claude Desktop will increase.

<figure><img src="../../.gitbook/assets/Screenshot 2025-03-28 at 16.46.59.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
There may be a error on top right corner, but it can be safely ignored. It is because of port binding, as Claude creates two processes for each MCP Server.
{% endhint %}

### Connection to cheqd network

The server automatically connects to the cheqd testnet using the provided mnemonic. This connection is used for all DID operations and credential management.

#### Verifying Network Connection

To verify the connection to the cheqd network, you can use the server to create and resolve a DID. You can ask the following to the AI Agent:

> "Can you create a DID on the cheqd network?"

If configured correctly, the server will create a new DID and then display its complete DID document, confirming proper connection to the cheqd network.

## Next Steps

Now that you have the Cheqd MCP Server running, you can:

* Explore the available tools for DID management and credential operations
* Create your first verifiable credential using the provided tools
* Set up a complete identity verification workflow

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Credentials</strong></mark></td><td>Issue Verifiable Credentials signed by cheqd DIDs using MCP Toolkit.</td><td><a href="issue-a-verifiable-credential.md">issue-a-verifiable-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Verify Credentials</strong></mark></td><td>Verify a Credential using MCP Toolkit.</td><td><a href="verify-a-credential.md">verify-a-credential.md</a></td></tr></tbody></table>

