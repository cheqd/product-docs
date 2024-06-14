# Search Status List v2021

The `/credential-status/search` API allows users to search for specific entries of a Status List on the cheqd network. For example, if there are multiple Status Lists associated with the same DID, the search functionality allows applications to make requests only to a specified Status List.&#x20;

## Step 1: Set Status List Search Filters

Users are able to filter by:

<details>

<summary>did (required)</summary>

The is the DID of the publisher of the Status List. The Status List entries will be DID-Linked Resources associated with this DID.

</details>

<details>

<summary>statusPurpose</summary>

The is the type of Status List to be queried. The options are:

* revocation
* suspension

</details>

<details>

<summary>statusListName</summary>

The is the name of the Status List. Alongside the statusPurpose, this will uniquely identify a version of the Status List.

</details>

## Step 2: Hit the API

Execute the filters from Step 1 on the API below:

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json" path="/credential-status/search" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger-api.json)
{% endswagger %}
