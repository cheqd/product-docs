# Schemas

## Overview

cheqd [on-ledger Resources](./) facilitate the use of schemas written directly on-ledger and referenceable via DIDs and DID Documents. &#x20;

{% hint style="success" %}
We want to **improve** the way **schemas are managed**&#x20;
{% endhint %}

JSON/JSON-LD Credential schemas are generally stored off-ledger on a web domain called [schema.org](https://schema.org). This enables different types of Credential schemas to be specifically defined, whilst often slotting into pre-defined contexts.&#x20;

[schema.org](https://schema.org), therefore, is healthy for interoperability since it pushes issuers of Credentials towards the use of commonly understood and publicly searchable schemas. However, there are a number of issues resulting from this way of managing schemas, largely stemming from the centralised nature of the existing solutions.

### Centralisation

Web domains present single-points of failure.&#x20;

There are multiple approaches to decentralised identity which use centralised infrastructure, including:&#x20;

* Schemas, stored on centralised databases such as schema.org&#x20;
* DID methods, such as did:web and did:git, where trust is based on Web 2 infrastructure&#x20;
* Revocation lists maintained on servers, such as Status List 21&#x20;

If, for example, any of the infrastructure listed above was to experience downtime due to technical faults, a dDoS attack or a failure of a cloud hosting provider, this could hugely disrupt the proper use of Verifiable Credentials in a production environment.

### **Link Rot**

Credential schemas, once defined, should be able to be used and referenced for many years, without being changed. **** For this reason, keeping an up-to-date version of the links themselves is crucial. Moreover, if the schema locations are moved and the links are broken, the Credentials issued become far less trustworthy. This is widely known as ‘**link rot’**.

### **Tamper Resistance**

The way schemas are currently stored is not immutable. Schemas may be changed and previous schema definitions may be purged from the database. This means if you had a Verifiable Credential issued in 2015 which referred to a particular schema - in 2022, that schema definition may have significantly changed. Once again, this erodes a layer of trust and permanence in the Verifiable Credential data model.

## **cheqd Schema Architecture**

cheqd Resources documentation uses schemas for its examples, so we will not replicate the content:

{% content-ref url="./" %}
[.](./)
{% endcontent-ref %}

cheqd will formally register two schema types to the [DID Spec Registries](https://www.w3.org/TR/did-spec-registries/):

* CL-Schema
* JSONSchema2020

Parties will be able to use cheqd [on-ledger Resources](./) to anchor other types of schemas, as they are supplied client side, however, we recommend that the schemas used should be formally registered in the [DID Spec Registries](https://www.w3.org/TR/did-spec-registries/) first.&#x20;

## Create schema

{% content-ref url="../../tutorials/resources/cheqd-cosmos-cli/create-resource.md" %}
[create-resource.md](../../tutorials/resources/cheqd-cosmos-cli/create-resource.md)
{% endcontent-ref %}
