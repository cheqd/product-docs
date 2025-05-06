---
description: Update a did:cheqd Decentralized Identifier (DID) using the ACA-Py Agent.
---

# Update a DID

Follow these instructions to update a did:cheqd DID from ACA-Py Agent.

> ⚠️ **Important...**
>
> Your wallet must have the key(s) which were generated when the DID was created, without them signing will fail.

## Update DID

To update an existing DID, you must pass the `did` id and the complete `didDocument`in the request body. The main field that is updated using this method os the `service` object of the DID.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/did/cheqd/update" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

