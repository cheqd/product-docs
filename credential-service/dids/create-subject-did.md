# Create Identity Keys and Subject DIDs

## Create Identity Keys

Issuers may want to create identity keypairs for multiple reasons, such as for signing payloads or for creating `did:key` DIDs.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/key/create" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

There is also an option to fetch an identity keypair by inputting a Key ID (kid) as a request format.

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/key/{kid}" method="get" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

## Creating a Subject DID

Credential Service currently supports two types of `subject` DIDs:

* `did:key`
* `did:vda`

With the former, you can follow the [did:key specification](https://w3c-ccg.github.io/did-method-key/) to create a subject DID based on a generated keypair.&#x20;

With the latter, you can setup your [did:vda](https://news.verida.io/introducing-did-vda-a-fast-cheap-web3-identity-solution-on-polygon-5d1487941477) subject DID on your [Verida wallet](https://www.verida.io/). Using the Credential Service, you will be able to send credentials to your Verida wallet and use it to store and securely back them up.
