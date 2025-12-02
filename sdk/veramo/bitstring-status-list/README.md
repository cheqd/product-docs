---
description: >-
  Create Bitstring Status Lists, stored as DID-Linked Resources (DLRs) on cheqd,
  using Veramo.
---

# Bitstring Status List

Bitstring Status List is a working document from the W3C to support a privacy-preserving, space-efficient, and high-performance mechanism for publishing status information such as suspension or revocation of JSON and JSON-LD Verifiable Credentials.

Using DID-Linked Resources, we have been able to natively support Bitstring Status List on cheqd directly.

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Status List</strong></mark></td><td>Create a Status List on-ledger as a DID-Linked Resource using Veramo</td><td><a href="bitstring-statuslist-resources.md">bitstring-statuslist-resources.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Status List within a Verifiable Credential</strong></mark></td><td>Issue a Verifiable Credential with a cheqd Status List within the body.</td><td><a href="issuing-a-verifiable-credential-referencing-status-list.md">issuing-a-verifiable-credential-referencing-status-list.md</a></td></tr></tbody></table>

## Understanding Bitstring Status List

Bitstring Status List utilises [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

![Graphic showing the Bitstring Status List bitstring](<../../../.gitbook/assets/StatusList21 Bitstring.png>)

_**Figure 1**: Graphic showing the Bitstring Status List bitstring_

Each issued Credential correlates with a position and index on the bitstring, so that a verifier will be able to correlate the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

### Where is the Status List usually published?

The issuer keeps a bitstring list of all Verifiable Credentials it has issued. The Status List is usually published by the issuer **in the format of its own Verifiable Credential.** This Verifiable Credential is generally hosted publicly on a centralised server or domain to enable third-party read-access.

### Where does cheqd store the Status List?

cheqd stores each Status List and subsequent entries on-ledger as [DID-Linked Resource](../../../architecture/adr-list/adr-002-did-linked-resources.md) versions. This has notable benefits, including the provenance, legitimacy and security of the Status List. For a full list of benefits, see [the context for creating DID-Linked Resources](../../../studio/did-linked-resources/understanding-dlrs/context.md).

## Alternatives

Below are a list of alternatives for creating cheqd Status Lists.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="../../../getting-started/studio/">studio</a></td><td><a href="../../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td></tr><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../../advanced/did-registrar/">did-registrar</a></td><td><a href="../../../.gitbook/assets/DIF.png">DIF.png</a></td></tr></tbody></table>
