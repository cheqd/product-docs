---
description: Create a Verifiable Credential Status List on-ledger
---

# 🎋 Create Status Lists

Credential Status Lists are generally sets of indices which can be used to mathematically derive whether an issued Credential has been revoked, suspended or is still valid. Status Lists are crucial for verifier applications to determine whether to accept a credential, presented by a holder.

## Get started

There are two predominant Status List formats supported in cheqd Studio. Please choose a Status List type below to get started.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Status List v2021</strong></mark></td><td>This Status List is a generic Bitstring Status List used for most JSON and JSON-LD credential implementations.</td><td><a href="status-list-v2021/">status-list-v2021</a></td></tr><tr><td><mark style="color:blue;"><strong>Token Status List</strong></mark></td><td>This Status List is generally used for SD-JWT specific credential types.</td><td><a href="token-status-list/">token-status-list</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for creating cheqd Status Lists.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>DID Registrar</strong></mark></td><td>Simple setup for building cheqd DIDs into existing applications using REST APIs, building into the <a href="https://uniregistrar.io/">Universal Registrar</a>.</td><td><a href="../../advanced/did-registrar/">did-registrar</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo-plugin/">veramo-plugin</a></td></tr></tbody></table>

t
