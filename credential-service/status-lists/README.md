---
description: Create a Verifiable Credential Status List v2021 on-ledger
---

# 🎋 Create Status Lists

Credential Status Lists are on-ledger objects which can be used to mathematically derive whether an issued Credential has been revoked, suspended or is still valid.&#x20;

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create Status List</strong></mark></td><td>Create an on-ledger Verifiable Credential Status List v2021 over REST API.</td><td><a href="create-status-list.md">create-status-list.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Update Status List</strong></mark></td><td>Update an on-ledger Verifiable Credential Status List v2021 over REST API.</td><td><a href="update-status-list.md">update-status-list.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Check Status List</strong></mark></td><td>Check an on-ledger Verifiable Credential Status List v2021 to determine whether a particular Credential is revoked or suspended.</td><td><a href="query-status.md">query-status.md</a></td></tr></tbody></table>

## Understanding Status List v2021

The [Status List 2021 Specification](https://w3c-ccg.github.io/vc-status-list-2021/) utilises [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

![Graphic showing the StatusList2021 bitstring](<../../.gitbook/assets/StatusList21 Bitstring.png>)

_**Figure 1**: Graphic showing the StatusList2021 bitstring_

Each issued Credential correlates with a position and index on the bitstring, so that a verifier will be able to correlate the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

### Where is the StatusList usually published?

The issuer keeps a bitstring list of all Verifiable Credentials it has issued. The StatusList is usually published by the issuer **in the format of its own Verifiable Credential.** This Verifiable Credential is generally hosted publicly on a centralised server or domain to enable third-party read-access.

### Where does cheqd store the Status List?

cheqd stores each Status List and subsequent entries on-ledger as [DID-Linked Resource](../../architecture/adr-list/adr-002-did-linked-resources.md) versions. This has notable benefits, including the provenance, legitimacy and security of the Status List. For a full list of benefits, see [the context for creating DID-Linked Resources](../did-linked-resources/understanding-dlrs/context.md).

## Alternatives

Below are a list of alternatives for creating cheqd Status Lists.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr></tbody></table>

t
