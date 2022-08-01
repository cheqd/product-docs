# DID URL Dereferencing

## Overview

The primary documentation for DID resolution is here:

* [DID Core v1.0](https://www.w3.org/TR/did-core/)
* [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/)
* [DID Spec Registries](https://www.w3.org/TR/did-spec-registries/)

### What is DID URL Dereferencing?

In simple terms, DID URL Dereferencing is when a DID resolver is able to parse a DID URL in order to be directed to a specific **resource**. A **resource** in this context is the content within a **service endpoint**.

In the [**DID Specification Registries**](https://www.w3.org/TR/did-spec-registries/), the two **service types** of which can be referenced are **LinkedDomains** and **DIDCommMessaging**. For example, a LinkedDomain could be a URL, a website, or another DID URL. However, within the [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/) specification, there are also references to `AgentService` , `MessagingService` , `VerifiableCredentialService` , `HubService` and `ProxyService`.
This suggests that the scope of what can be a `type` within the `service` section, is currently unstandardised and broad. As a general statement, the community should strive to record the core types used within the [**DID Specification Registries**](https://www.w3.org/TR/did-spec-registries/)**.** In cheqd's DID architecture, we will be using the `service` \*\*\*\* field to dereference DID URLs in order to fetch **schemas,** using the `service` type: `CL-Schema` or `JSONSchema`. These schemas will be stored using DID URLs on the cheqd network. As such, DID URL Dereferencing is important for us to be able to correctly point to and fetch schemas.

## What is the difference between DID Resolution and URL Dereferencing?

When you **resolve** a DID you get a DID Document. When you **dereference** a DID, you may get the DID Document, a portion of a DID document, or the resource at the end of a service endpoint (if the DID contains a service component).You can only **resolve** a DID to return a DID document, and you can only **dereference** a DID reference to return whatever resource is referenced. The default dereference (assuming no additional relative component like a path, fragment, query or service) should return the full DID document.

### [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/): Dereferencing flows for Primary resource

### What is a primary resource?

According to [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/), a primary resource is the information that a URL directly references. There are **three compositions** of Primary Resource described in this specification:

#### 1. Service or relativeRef parameter present

If the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url) contains the [DID parameter](https://www.w3.org/TR/did-core/#did-parameters) `service` and optionally the `relativeRef` DID parameter.

For example:

 ```bash
did:example:1234?service=files&relativeRef=%2Fmyresume%2Fdoc%3Fversion%3Dlatest
```

or;

```bash
did:example1234?service=resource
```

Outcome: From the resolved [DID document](https://w3c-ccg.github.io/did-resolution/#dfn-did-document), select the [service endpoint](https://w3c-ccg.github.io/did-resolution/#service-endpoint-construction) whose `id` property contains a fragment which matches the value of the `service` DID parameter of the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url). This is called the input [service endpoint](https://w3c-ccg.github.io/did-resolution/#dfn-service-endpoint).

#### 2. No path and no query

If the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url) contains no [DID path](https://w3c-ccg.github.io/did-resolution/#dfn-did-path) and no [DID query](https://w3c-ccg.github.io/did-resolution/#dfn-did-query)

For example:

```bash
did:example:1234
```

Outcome: Return the resolved [DID document](https://w3c-ccg.github.io/did-resolution/#dfn-did-document).

#### 3. Input contains DID path and/or DID query

If the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url) contains a [DID path](https://w3c-ccg.github.io/did-resolution/#dfn-did-path) and/or [DID query](https://w3c-ccg.github.io/did-resolution/#dfn-did-query):

For example:

```bash
did:example:1234/custom/path?customquery
 ```

Outcome:

- The applicable [DID method](https://w3c-ccg.github.io/did-resolution/#dfn-method) _MAY_ specify how to dereference the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url).
- The client _MAY_ be able to dereference the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url) in an application-specific way.

#### 4. Not able to dereference

If neither this algorithm, nor the applicable [DID method](https://w3c-ccg.github.io/did-resolution/#dfn-method), nor the client is able to dereference the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url):

- Return a null result.

### [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/): DID URL Deferencing flows for Secondary resource

#### What is a Secondary resource?

Seemingly, a Second resource is a resource identified by a **fragment (#)**, rather than a path, query or service reference. According to the spec [DID Resolution v0.2](https://w3c-ccg.github.io/did-resolution/):

#### 1. If the DID URL contains a fragment but no path/query

Input: If the DID URL contains a fragment, then it is referring to a specific item within the DID Document, for example:

```bash
did:example:1234#keys-1
```

Outcome: This DID URL is referring to a specific key listed in the DID Document. As such, to dereference this URL, the DID Document should be fetched, and the #keys-1 selected whose `id` property matches the input

#### 2. DID URL contains a fragment and queries a Service endpoint

Input: if the result of [4.2 Dereferencing the Primary Resource](https://w3c-ccg.github.io/did-resolution/#dereferencing-algorithm-primary) is an output [service endpoint](https://w3c-ccg.github.io/did-resolution/#dfn-service-endpoint) URL, and the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url) contains a [DID fragment](https://w3c-ccg.github.io/did-resolution/#dfn-did-fragment), for example:

```bash
did:example:1g2eub9t35y935y9?service=files&relativeRef=%2Fmyresume%2Fdoc%3Fversion%3Dlatest#intro
```

Outcome:

- Append the [DID fragment](https://w3c-ccg.github.io/did-resolution/#dfn-did-fragment) to the output [service endpoint](https://w3c-ccg.github.io/did-resolution/#dfn-service-endpoint) URL. In other words, the output [service endpoint](https://w3c-ccg.github.io/did-resolution/#dfn-service-endpoint) URL "inherits" the [DID fragment](https://w3c-ccg.github.io/did-resolution/#dfn-did-fragment) of the input [DID URL](https://w3c-ccg.github.io/did-resolution/#dfn-did-url).
- Return the output [service endpoint](https://w3c-ccg.github.io/did-resolution/#dfn-service-endpoint) URL.
