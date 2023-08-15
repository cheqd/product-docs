# Verify Presentation

## Verify Verifiable Presentations

A Verifiable Presentation is a collection of multiple Verifiable Credentials that are being presented by a `holder` to a `verifier`. In addition to checking whether the Credentials are untampered, Verifiable Presentation verification also checks that the `holder` subject DID is valid.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/presentation/verify" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
