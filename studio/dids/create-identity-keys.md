---
description: >-
  Create a standalone public/private keypair, used to create Decentralized
  Identifiers (DIDs), using cheqd Studio.
---

# Create Identity Keys

In decentralized identity systems, all actors—whether Issuers, Holders, or Verifiers—require cryptographic keys to prove control over a **Decentralized Identifier (DID)** and to sign or verify interactions (e.g. credential issuance or presentation).

This tutorial guides you through generating a **key pair** that can be used to:

* ✅ Create a new DID (e.g. `did:cheqd` or `did:key`)
* ✅ Import into **cheqd Studio** for identity operations
* ✅ Use directly in SDKs or agent frameworks that support key-based identity

***

## Why Identity Keys Matter

DIDs are built on public-private key cryptography. The **public key** is embedded in or referenced by the DID Document, while the **private key** remains securely held by the actor controlling the identity.

Without keys, you can’t:

* Create a DID
* Authenticate or prove control of an identity
* Sign or verify credentials and presentations
* Rotate or deactivate identifiers

***

## Step 1: Choose Your Key Type

cheqd Studio supports two cryptographic key types for identity operations:

| Key Type    | Use Case / Notes                                                                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Ed25519`   | The default and recommended key type for most DID and Verifiable Credential operations. Fast, secure, and widely supported across agent frameworks.                     |
| `secp256k1` | A widely adopted elliptic curve used in blockchain ecosystems (e.g. Bitcoin, Ethereum). Useful for cross-chain identity interoperability or aligning with Web3 wallets. |

> ✅ Choose `Ed25519` for identity-specific use cases. Use `secp256k1` if you need compatibility with blockchain-based tooling, wallets, or ecosystems that already rely on this curve.

## Step 2: Hit the API below

Use the API below to create and generate your new identity key pair of either type `Ed25519` or `secp256k1.`

{% openapi-operation spec="cheqd-studio-api" path="/key/create" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

## Step 3 (Alternative): Fetch Identity Key Details

If you've already generated a key pair using cheqd Studio and want to **retrieve, reference or use it later**, you can do so by referencing its **Key ID (`kid`)**. This is useful for:

* Recovering key material generated earlier (e.g., to construct a DID or sign a credential)
* Working with shared or externally provisioned keys
* Avoiding duplicate key creation when managing identities programmatically

{% openapi src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/key/read/{kid}" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endopenapi %}
