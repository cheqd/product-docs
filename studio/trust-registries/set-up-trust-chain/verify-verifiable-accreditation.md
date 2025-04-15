# Verify Verifiable Accreditation

Users are able to **issue Verifiable Accreditations** to determine the validity of the accreditation, as well to traverse the trust chain to a root of trust.&#x20;

## Step 1: Issue Accreditation

Make sure you have already created at least one Verifiable Accreditation that you are able to query in the verify API.

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Issue Verifiable Accreditation</strong></mark></td><td>Issue a Verifiable Accreditation as a DID-Linked Resource from an "issuer" DID to a recipient "subject" DID.</td><td><a href="issue-accreditation.md">issue-accreditation.md</a></td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

## Step 2: Use the Verify Accreditation API

{% swagger src="https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json" path="/trust-registry/accreditation/verify" method="post" %}
[https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json](https://raw.githubusercontent.com/cheqd/studio/refs/heads/main/src/static/swagger-api.json)
{% endswagger %}

