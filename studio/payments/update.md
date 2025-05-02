# Bulk Update or Rotate Encryption Keys

The `/credential-status/update/encrypted` API enables users to update the indices of a Status List or rotate the encryption keys. This may be useful for revoking Credentials in bulk, rather than submitting individual `/credential/revoke` requests.

When a new encrypted Status List resource is published, this will also encrypt the latest version with a new set of encryption keys. This may therefore be used to rotate encryption keys, even if the listed indices are kept the same.

{% openapi-operation spec="cheqd-studio-api" path="/credential-status/update/encrypted" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
