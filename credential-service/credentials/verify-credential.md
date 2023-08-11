# Verify Credential

## Verify Verifiable Credentials

Once you have issued your credential and have a JWT as part of the credential proof, you can use the `/credential/verify` API to check that the JWT has not been tampered.

Simply paste the JWT or the full credential body which you obtain from the response of the `/credential/issue` Credential API into the request field, and the API will give you a response including the following verification policies:

1. Whether the Credential has been tampered
2. Whether the Credential has a valid issuance date
3. Whether the Credential has expired
4. Whether the Credential Status is valid

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/verify" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

