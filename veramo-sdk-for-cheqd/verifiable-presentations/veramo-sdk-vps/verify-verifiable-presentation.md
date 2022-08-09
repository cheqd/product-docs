# Verify Verifiable Presentation

## Overview

This tutorial offers step-by-step guidance on how to verify a [Verifiable Presentation](https://w3c-ccg.github.io/vp-request-spec/).

The verification in this tutorial will be using the `jwt` proof of a JSON verifiable presentation, created previously in the `create verifiable presentation` tutorial.

The verifier of the Verifiable Presentation will be the issuer,holding the DID that signed the the credential, used by the subject (holder) that generated the presentation.

## Pre-requisites

For full information on the architecture, setup and cofig, check [`did-provider-cheqd`](https://github.com/cheqd/did-provider-cheqd).

>Ensure you have saved the [`agent.yml`](https://raw.githubusercontent.com/cheqd/did-provider-cheqd/main/agent.yml) file in your local project directory.

## Steps

### Step 1

Begin the verification process, entering the txt. file name before the veramo presentation verify command:

```bash
cat pres.txt | veramo presentation verify
```

### Step 2

If the presentation can be verified, the output will demonstrate this with the response:

```bash
Presentation verified successfully 
```