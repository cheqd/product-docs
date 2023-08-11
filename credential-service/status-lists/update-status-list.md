---
description: Manually update indices in Status List
---

# Update Status List

The `/credential-status/update` API enables users to manually update the inidices of a Status List. This may be useful for revoking Credentials in bulk, rather than submitting individual `/credential/revoke` requests.

This may also be used for re-encrypting a StatusList if the Issuer wants to rotate or change the encryption.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json" path="/credential-status/update" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/swagger-host/src/static/swagger.json)
{% endswagger %}
