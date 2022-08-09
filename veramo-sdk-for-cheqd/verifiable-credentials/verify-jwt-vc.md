# Verify a JSON based JWT Verifiable Credential

## Overview

This tutorial offers step-by-step guidance on how to verify a [JSON credential](https://www.w3.org/TR/vc-data-model/#json), encoded as a [JWT (JSON Web Token)](https://www.w3.org/TR/vc-data-model/#json-web-token), a [W3C compliant proof format.](https://www.w3.org/TR/vc-data-model/#proof-formats)

Verifying a Verifiable Credential can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

## Pre-requisites

To verify a Verifiable Credential you will need to first issue one to a subject. See the tutorial for issuing a Verifiable Credential for this.

For full information on the architecture, setup and config, check [`did-provider-cheqd`](https://github.com/cheqd/did-provider-cheqd).

## Steps

### Step 1

Begin credential verification, entering the json file name **before** the veramo credential verify command:

```bash
cat cred.json | veramo credential verify
```

### Step 2

If the credential can be verified correctly, the output will demonstrate this with the response:

```bash
Credential verified successfully
```

To test this, feel free to tamper with the JWT. If the credential cannot be verified you will see the response:

```bash
Credential could not be verified 
```

Alternative verification options.

It is also possible to verify the credential using the raw credential or JWT.

To do this use:

```bash
Veramo credential verify --raw
```

Then enter the JWT or entire JSON credential including the JWT.
