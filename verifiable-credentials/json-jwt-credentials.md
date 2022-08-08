# JSON / JWT Credentials

## Background

**JSON Web Token (JWT)** is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object.

This information can be verified and trusted because it is digitally signed.

JWTs are widely used in legacy identity infrastructure, underlying the frameworks used by [OAuth 2.0](https://oauth.net/2/) and [OpenID Connect](https://openid.net/connect/). As such, there is a plethora of software libraries and toolkits for developers to build upon using JWT, making their adoption far more seamless than other Verifiable Credential formats.

## Issuing JWT-VCs anchored on cheqd

[`did-jwt-vc`](https://github.com/decentralized-identity/did-jwt-vc) is a library that enables users to create and verify W3C Verifiable Credentials and Presentations in JWT format.

Since this library is used by the [Veramo SDK](https://veramo.io), we have tutorials on cheqd to demonstrate how this can be used.
