---
description: >-
  Create a 'Holder' or 'Subject' Decentralized Identifier (DIDs), of method
  did:key using cheqd Studio.
---

# Create Subject DID

In decentralized identity systems, **Holders** (also referred to as **Subjects**) are the recipients of **Verifiable Credentials**—typically representing individuals, organisations, objects or devices.

To enable credential interactions such as **receiving**, **presenting**, and **proving control** over an identity, each Holder needs a unique cryptographic key pair. This key pair is used to create a **Decentralized Identifier (DID)** that represents their identity.

***

## Use Cases for Holder Identity Keys

After generating a key pair using the API below, you can use it to:

* ✅ **Generate a `did:key` identifier**, an off-ledger, self-contained DID that is ideal for lightweight identity use cases

***

## Why Use `did:key` for Holders?

The `did:key` method is a simple, deterministic DID method that embeds the Holder’s public key directly into the DID itself. It doesn’t require blockchain anchoring, making it:

* ⚡ **Fast and lightweight** – no on-ledger operations required
* 🔐 **Privacy-preserving** – good for ephemeral or throwaway identifiers
* 🧪 **Useful for testing and interoperability** – widely supported by wallets and agent frameworks
* 🧍 **Ideal for Holder/Subject identities** – where the main role is to receive and present credentials rather than issue them

Many ecosystems choose `did:key` for **wallet-based identities**, while keeping issuer DIDs on-ledger (e.g. `did:cheqd`) to ensure resolvability and public trust.

Take a deeper look into the `did:key` specification here:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a  did:key DID</strong></mark></td><td>Create an off-ledger 'holder' or 'subject' DID, of the <code>did:key</code> method using the specification.</td><td><a href="https://w3c-ccg.github.io/did-key-spec/">https://w3c-ccg.github.io/did-key-spec/</a></td></tr></tbody></table>

{% hint style="success" %}
Note that there are also perfectly valid use cases for using a `did:cheqd` DID for a 'Holder' or 'Subject' DID as well, where it is valuable to have the 'Holder' / 'Subject' identifiable on-chain.&#x20;

For example, AI Agents, Organisations or products in a supply chain are good use cases for using `did:cheqd`. &#x20;

This is because the 'Holder' / 'Subject' does not have Personally Identifiable Information (PII) associated with it.
{% endhint %}

***

## Get Started

To create a `did:key` DID, you can generate an identity key pair using the API below on cheqd Studio.

{% openapi-operation spec="cheqd-studio-api" path="/key/create" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
