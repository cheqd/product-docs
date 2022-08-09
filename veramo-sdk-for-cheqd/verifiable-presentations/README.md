# Create Verifiable Presentation

## Overview

This tutorial offers step-by-step guidance on how to create a [Verifiable Presentation](https://w3c-ccg.github.io/vp-request-spec/).

Creating a Verifiable Presentation can be achieved through both the Veramo CLI, or directly through an application, setup to read and write to the cheqd ledger, using the Veramo SDK for cheqd.

## Pre-requisites

A Verifiable Presentation will always be created by the holder themselves. Therefore this part will use the `did:key` that was created for the subject.

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`](https://github.com/cheqd/did-provider-cheqd).

>Ensure you have saved the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file in your local project directory.

## Steps

### Step 1

Begin creating a Verifiable Presentation with:

```bash
veramo presentation create
```

### Step 2

Specify the subject to be used for the Variable Presentation creation (i.e. chosen holder `did:key`)

### Step 3

Specify the `Tag` for the Verifiable Presentation.

To accept the default hit enter.

### Step 4

Specify the Verifier ID.

In this case the verifier is the issuer (as the verifier is requesting information from the issuer to ascertain that the credential has been issued by the legitimate authority.

Select the issuer DID you would like to use from the issuer DIDs presented.

### Step 5

Specify the Presentation type. This can be the same as the Verifiable Credential or a subpart of it.

### Step 6

Select credential you would like to use to create your Verifiable Presentation. If you have not yet created a credential, use the tutorial for creating a verifiable credential.

### Step 7

Specify whether you would like to add another credential.

A presentation can use claims from multiple presentations.

### Step 8

You’ll now see your Verifiable Presentation, both in the JSON format and JWT.

Save the Verifiable Presentation JWT.  

### Step 9

Save the JWT as a txt file under the root directory of your project.
