---
description: Deactivate a did:cheqd DID using the ACA-Py Agent.
---

# Deactivate a DID

Follow these instructions to deactivate a did:cheqd DID from ACA-Py Agent.

> ⚠️ **Important...**
>
> Your wallet must have the key(s) which were generated when the DID was created, without them signing will fail.

## Deactivate DID

To deactivate an active DID, pass the `did`id in the request body, with any additional `options`that you may have configured.

{% openapi src="../../../.gitbook/assets/swagger.json" path="/did/cheqd/deactivate" method="post" %}
[swagger.json](../../../.gitbook/assets/swagger.json)
{% endopenapi %}

