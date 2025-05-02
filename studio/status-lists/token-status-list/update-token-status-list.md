---
description: Update a Token Status List JWT or CWT as a new DID-Linked Resource
---

# Update Token Status List

## Step 1. Manually create your new Status List JWT/CWT and save the file locally

Create a JWT or CWT file with the updated indices and bits for the updated Status List.

<details>

<summary>Non-normative example for a Status List Token in JWT format</summary>

```json
{
  "alg": "ES256",
  "kid": "12",
  "typ": "statuslist+jwt"
}
.
{
  "exp": 2291720170,
  "iat": 1686920170,
  "iss": "https://example.com",
  "status_list": {
    "bits": 3,
    "lst": "eNrbuRgAAhcBXQHutdpRiBFREinSjQfeTpXmdQfwefegD"
  },
  "sub": "https://example.com/statuslists/1"
}
```

</details>

<details>

<summary>Non-normative example for a Status List Token in CWT format (not including the type header yet)</summary>

```json
d28453a20126106e7374617475736c6973742b637774a1044231325860a502782168
747470733a2f2f6578616d706c652e636f6d2f7374617475736c697374732f310173
68747470733a2f2f6578616d706c652e636f6d061a648c5bea041a8898dfea19fffe
56a2646269747301636c73744a78dadbb918000217015d58400f2ca3772e10b09d5d
6ed56461f7cba1a816c6234072d1bb693db277048e5db5a4e64444492a9b781d6c7a
c9714db99cc7aadb3812ec90cab7794170bab5b473
```

</details>

Save this file locally and call it something like `statusListTokenUPDATED.json`

{% hint style="info" %}
Note that each JWT or CWT must be **below \~45kb in size**.
{% endhint %}

## Step 2: Encode the updated file

Prepare a file with resource and encode it into `base64, base64url` or `hex`. On Unix systems, you can use the following command input:

```bash
$ base64 -w 0 resource.json
<path-to-the-resource-file>
```

Expected output:

```bash
$ base64 -w 0 resource.json
HfSynOpmBrhgfYguING
```

## Step 3: Set the same Resource Name and Type

To create a new version you must use the **same "name" and "type"** for your resource, and ensure that the new Token Status List resource is being created **underneath the same DID** as your initial DI&#x44;**.** You will also need to be logged into the same cheqd Studio account that you used to create the initial Token Status List to have access to the keys to sign the update.

For Token Status Lists, the "type" MUST be: **`"TokenStatusList"`.**

For example:

```json
{
    "data": "HfSynOpmBrhgfYguING",
    "encoding": "base64url",
    "name": "DegreeCredentialStatus",
    "type": "TokenStatusList"
}
```

## Step 4: Populate the request inputs and hit the API

{% openapi-operation spec="cheqd-studio-api" path="/resource/create/{did}" method="post" %}
[Broken link](broken-reference)
{% endopenapi-operation %}
