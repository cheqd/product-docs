---
description: >-
  Charge for Verifiable Credentials and initiate verifier-pays-issuer payment
  flows.
---

# ♻️ Charge for Verifiable Credentials

Credential Payments brings a significant evolution to the Verifiable Credentials landscape, allowing **verifiers to directly pay issuers to unlock Credential Status information**. Underneath the hood, we've engineered a robust and secure payment flow that streamlines the end-to-end payment process, with accuracy, speed, and cryptographic integrity.

## Get started

Create encrypted Status Lists on-ledger, and pay to unlock the Access Control Conditions in CHEQ, using our **cheqd Studio Payments APIs**:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Charge for Status List</strong></mark></td><td>With Credential Payments, Issuers are able to create payment-gated Status Lists in order to charge for the verification of Credential Status information.</td><td><a href="charge.md">charge.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Credential with Encrypted Status List</strong></mark></td><td>Issue a Verifiable Credential referencing an encrypted Status List in the body, with a specified bitstring index.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Verifier pays Issuer</strong></mark></td><td>Understand how a Verifier pays an Issuer in CHEQ to unlock Status List and verify Credential Status.</td><td><a href="verifier-pays-issuer.md">verifier-pays-issuer.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Bulk Update or Rotate Encryption Keys</strong></mark></td><td>Update a set of Credential indices or rotate the encrypted symmetric key for the Status List.</td><td><a href="update.md">update.md</a></td></tr></tbody></table>

## Learn more

Build your understanding of How Credential Payments work, including Access Control Conditions and how the model preserves privacy.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Understanding Credential Payments</strong></mark></td><td>Before diving into Credential Payments, developers may want to learn how the Payment gating and Access Control condition logic works.</td><td><a href="learn/">learn</a></td></tr><tr><td><mark style="color:blue;"><strong>Why are we payment gating Status Lists?</strong></mark></td><td>Learn about the role of cheqd in Trusted Data Markets and why we are payment gating the "reputability" of Credentials.</td><td><a href="https://cheqd.io/blog/the-role-of-cheqd-in-trusted-data-markets/">https://cheqd.io/blog/the-role-of-cheqd-in-trusted-data-markets/</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for using Credential Payments.

<table data-card-size="large" data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden data-card-cover data-type="files"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Veramo SDK Plugin</strong></mark></td><td>The <strong>Veramo SDK Plugin</strong> is an extension of the Veramo SDK, a JavaScript framework for Trusted Data, adding support for cheqd functionality.</td><td><a href="../../sdk/veramo/">veramo</a></td><td><a href="../../.gitbook/assets/veramo logo.png">veramo logo.png</a></td></tr></tbody></table>
