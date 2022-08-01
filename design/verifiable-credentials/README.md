# ✅ Verifiable Credentials

## Overview

A Verifiable Credential (VC) is a tamper-evident credential that has authorship that can be cryptographically verified. Verifiable credentials can be used to build [verifiable presentations](https://www.w3.org/TR/vc-data-model/#dfn-verifiable-presentations), which can also be cryptographically verified. The [claims](https://www.w3.org/TR/vc-data-model/#dfn-claims) in a credential can be about different [subjects](https://www.w3.org/TR/vc-data-model/#dfn-subjects).

If you want to learn more about what [Verifiable Credentials are, please go over to our learning site here.](https://learn.cheqd.io/overview/introduction-to-decentralised-identity/what-is-a-verifiable-credential-vc)

## Verifiable Credential types

There are multiple different types of Verifiable Credentials, each with different compositions and syntaxes. The problem with different VCs is that they do not always interoperate with each other, and often need completely separate software to process them.

For this reason, cheqd intends to support all mainstream types of Verifiable Credential, in order to best support its partners building on the network:

{% content-ref url="json-based-jwt.md" %}
[json-based-jwt.md](json-based-jwt.md)
{% endcontent-ref %}

{% content-ref url="json-ld.md" %}
[json-ld.md](json-ld.md)
{% endcontent-ref %}

{% content-ref url="anoncreds/" %}
[anoncreds](anoncreds/)
{% endcontent-ref %}

## cheqd node SDK architecture and tooling

cheqd packages up its tools for creating and resolving Verifiable Credentials into a digestible toolkit. [Read more about cheqd's SDK architecture on our Product site.](https://product.cheqd.io/)