---
description: Update indices in Status List
---

# Update Status List

The `/credential-status/update` API enables users to update the indices of a Status List. This may be useful for revoking Credentials in bulk, rather than submitting individual `/credential/revoke` requests.

This may also be used for re-encrypting a Status List if the Issuer wants to rotate or change the encryption.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/update" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
