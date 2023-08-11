---
description: Querying Status List entries or indices
---

# Query Status List

Using the `/credential-status/check` API, users are able to query specific Credential indices within a Status List to ascertain whether the Credential is revoked, suspended or currently valid.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/check" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

The `/credential-status/search` API allows users to search for specific entries of a Status List on the cheqd network. For example, if there are multiple Status Lists associated with the same DID, the search functionality allows applications to make requests only to a specified StatusList.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/search" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
