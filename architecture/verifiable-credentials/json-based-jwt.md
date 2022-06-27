# JSON based JWT

**JSON Web Token (JWT)** is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object.&#x20;

This information can be verified and trusted because it is digitally signed.

JWTs are widely used in legacy identity infrastructure, underlying the frameworks used by [OAuth 2.0](https://oauth.net/2/) and [OpenID Connect](https://openid.net/connect/). As such, there is a plethora of software libraries and toolkits for developers to build upon using JWT, making their adoption far more seamless than other Verifiable Credential formats.&#x20;

## DID JWT VC

DID-JWT-VC is a library that enables users to create and verify W3C Verifiable Credentials and Presentations in JWT format.

{% embed url="https://github.com/decentralized-identity/did-jwt-vc" %}

cheqd has forked this library to include the ability to create and verify W3C Verifiable Credentials and Presentations in JWT format using cheqd DIDs. In time, cheqd intends to merge this fork back into the upstream did-jwt-vc repository to ensure alignment between cheqd's work and future updates to the main repo.&#x20;

cheqd's fork of this library can be found below:

{% embed url="https://github.com/cheqd/did-jwt-vc" %}

## Issue Credential using JSON based JWT

To start using this functionality, you can follow the tutorials here:

{% content-ref url="../../tutorials/verifiable-credentials/veramo-cli-sdk/json-based-jwt/issue-verifiable-credential.md" %}
[issue-verifiable-credential.md](../../tutorials/verifiable-credentials/veramo-cli-sdk/json-based-jwt/issue-verifiable-credential.md)
{% endcontent-ref %}

