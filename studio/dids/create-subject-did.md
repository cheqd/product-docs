---
description: >-
  Create holder Decentralized Identifiers (DIDs), of method did:key, and
  associated identity keys using cheqd Studio.
---

# Create Identity Keys and Subject DIDs

Issuers may want to create identity keypairs for multiple reasons, such as for signing payloads or for creating `did:key` DIDs.

{% openapi-operation spec="cheqd-studio-api" path="/key/create" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}

There is also an option to fetch an identity keypair by inputting a Key ID (kid) as a request format.

{% openapi src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/key/read/{kid}" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endopenapi %}

## Creating a Subject DID

cheqd Studio currently supports one types of `subject` DIDs:

* `did:key`

With the former, you can follow the [did:key specification](https://w3c-ccg.github.io/did-key-spec/) to create a subject DID based on a generated keypair.&#x20;

