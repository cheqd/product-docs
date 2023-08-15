# Suspend or Unsuspend Credential

Users are also able to suspend Verifiable Credentials. The difference between revocation and suspension is that **suspended Credentials may be unsuspended at a future date**; whereas, **revoked credentials are permanently revoked**.

## Suspend Verifiable Credentials

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/suspend" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Unsuspend (reinstate) Verifiable Credentials

If a Credential has been suspended, and an Issuer wants to unsuspend the Credential to make it once again valid, the Issuer can **reinstate a suspended Credential**.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential/reinstate" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}
