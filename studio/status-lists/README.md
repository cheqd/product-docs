---
description: >-
  Create, update and fetch Status Lists on cheqd, stored as DID-Linked Resources
  (DLRs).
---

# 🎋 Create Status Lists

Status Lists are generally sets of indices which can be used to mathematically derive whether an issued Credential has been revoked, suspended or is still valid. Status Lists are crucial for verifier applications to determine whether to accept a credential, presented by a holder.

## What options do I have for creating status lists on cheqd?

There are many different ways to create status lists on cheqd, with options for easy integration (e.g. cheqd Studio) and more bespoke integrations (e.g. Credo and Veramo). Below are a list of options for creating cheqd DIDs.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>cheqd Studio</strong></mark></td><td>Our API product enables users to <strong>use cheqd's functionality with minimal complexity</strong> and easily integrate APIs into existing apps. </td><td><a href="../../.gitbook/assets/cheqd_logo_card.jpg">cheqd_logo_card.jpg</a></td><td><a href="../dids/create-did.md">create-did.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Credo</strong></mark></td><td>Credo is an SDK which <strong>supports the European Architecture and Reference Framework (ARF)</strong> <strong>standards as well as AnonCreds</strong> with full cheqd support for DIDs. </td><td><a href="../../.gitbook/assets/Credo.png">Credo.png</a></td><td><a href="../../sdk/credo/">credo</a></td></tr><tr><td><mark style="color:blue;"><strong>Veramo</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> supports JSON, JSON-LD credentials as well as cheqd Credential Payments in an SDK.</td><td><a href="../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td><td><a href="../../sdk/veramo/">veramo</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id Community Stack</strong></mark></td><td>Walt.id Community Stack is an SDK that supports the <strong>European Architecture and Reference Framework (ARF)</strong> standards for identity, with full cheqd support. </td><td><a href="../../.gitbook/assets/walt.id logo.png">walt.id logo.png</a></td><td><a href="../../sdk/walt-id.md">walt-id.md</a></td></tr></tbody></table>

## Get started with cheqd Studio

There are two predominant Status List formats supported in cheqd Studio. Please choose a Status List type below to get started.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Bitstring Status List</strong></mark></td><td>This Status List is a generic Bitstring Status List used for most JSON and JSON-LD credential implementations.</td><td><a href="bitstring-status-list/">bitstring-status-list</a></td></tr><tr><td><mark style="color:blue;"><strong>Token Status List</strong></mark></td><td>This Status List is generally used for SD-JWT specific credential types.</td><td><a href="token-status-list/">token-status-list</a></td></tr></tbody></table>
