# Verifiable Credentials and Presentations

{% hint style="info" %}
:books: **Learn about Verifiable Credentials and Presentations**

If you want to learn more about what [Verifiable Credentials and Presentations are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc)
{% endhint %}

## Get started

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Setup Veramo CLI</strong></mark></td><td>Before you get started, you need to install our Veramo packages and setup the CLI. </td><td><a href="../setup/">setup</a></td></tr><tr><td><mark style="color:blue;"><strong>Issue Credentials</strong></mark></td><td>Issue W3C conformant Verifiable Credentials over DIDComm.</td><td><a href="issue-credential.md">issue-credential.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Verify Credentials</strong></mark></td><td>Verify whether Credentials are valid, have been tampered or have expired.</td><td><a href="verify-jwt-credential.md">verify-jwt-credential.md</a></td></tr></tbody></table>

## Supported Credential types

Veramo SDK plugin supports two major digital Credential types. Below you can learn about these  Credential formats:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>JSON (JWT)</strong></mark></td><td><strong>JSON Web Token (JWT)</strong> Credentials are a simple way to transmit Trusted Data as a JSON object.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-jwt</a></td></tr><tr><td><mark style="color:blue;"><strong>JSON-LD</strong></mark></td><td><strong>JSON-LD (Linked Data)</strong> Credentials are a richer data format, allowing applications to follow embedded links to other pieces of Linked Data across the web.</td><td><a href="https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld">https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc/what-are-the-different-types-of-verifiable-credentials/json-ld</a></td></tr></tbody></table>

## Alternatives

Below are a list of alternatives for using Credentials with cheqd support. Each offers a different set of protocols and underlying technical capabilities.

<table data-view="cards" data-full-width="false"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Credential Service</strong></mark></td><td><strong>Credential Service</strong> is a SaaS product offering the easiest and quickest way to issue Credentials, using REST APIs and very little integration effort.</td><td><a href="broken-reference">Broken link</a></td></tr><tr><td><mark style="color:blue;"><strong>Aries Framework JavaScript</strong></mark></td><td>Aries Framework JavaScript is an SDK which <a href="https://hyperledger.github.io/anoncreds-spec/"><strong>supports ZKCreds (AnonCreds)</strong></a> natively with cheqd support. </td><td><a href="../../aries-framework-js.md">aries-framework-js.md</a></td></tr><tr><td><mark style="color:blue;"><strong>Walt.id SSI Kit</strong></mark></td><td>Walt.id SSI Kit is an SDK that supports the <a href="https://digital-strategy.ec.europa.eu/en/library/european-digital-identity-architecture-and-reference-framework-outline"><strong>European Architecture and Reference Framework (ARF)</strong></a> standards for identity, with full cheqd support. </td><td><a href="../../ssi-kit.md">ssi-kit.md</a></td></tr></tbody></table>
