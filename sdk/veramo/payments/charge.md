---
description: >-
  Charge for a Status List stored as a DID-Linked Resource (DLR) through Access
  Control Conditions, using Veramo.
---

# Charge for Status List

## **Charge for a Credential Status List**

To create an encrypted Verifiable Credential Bitstring Status List with a set of Access Control Conditions, an Issuer will need to:

1. Compile a `payload.json` file with requisite Access Control Conditions;
2. Submit a `create Status List` transaction the the ledger.

### Step 1: Create a DID

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Create a DID</strong></mark></td><td>Create a W3C conformant Decentralised Identifier using cheqd's DID Method (did:cheqd), using the Veramo SDK Plugin.</td><td><a href="../dids/create-did.md">create-did.md</a></td></tr></tbody></table>

### Step 2: Compiling a Bitstring Status List Payload file

Below are is an example of encrypted Status List Payload file:&#x20;

<details>

<summary>BitstringStatusList</summary>

```json
{
    "kms": "local",
    "issuerDid": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99",
    "statusListName": "status-list-encrypted",
    "statusPurpose": ["revocation", "suspension", "message"],
    "statusSize": 2,
    "statusMessages": [
		    { "status": "0x0", "message": "valid" },
		    { "status": "0x1", "message": "revoked" },
		    { "status": "0x2", "message": "suspended" },
		    { "status": "0x3", "message": "unknown" }
	    ],
    "encrypted": true,
    "paymentConditions": [
        {
            "type": "timelockPayment",
            "feePaymentAddress": "cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp",
            "feePaymentAmount": "147603000000000ncheq",
            "intervalInSeconds": 3153600000
        }
    ],
    "returnSymmetricKey": true,
    "dkgOptions": {
        "chain": "cheqdTestnet",
        "network": "datil-dev"
    }
}

```

</details>

The code snippet above shows a  Bitstring Status List Payload file which supports both Revocation and Suspension. With Bitstring Status List with `statusSize` as 2, the same list can be used for revocation, suspension or any other message as determined by the `statusMessages` . The payload indicates to the ledger that the Resource should be fully encrypted and only be decrypted upon the set conditions being met.

The Payload file is a crucial configuration element that establishes Payment Conditions for unlocking a Status List Resource. Let's break down the components of this code to understand how it achieves this access control mechanism:

<table><thead><tr><th width="248.33333333333331">Parameter</th><th>Value</th><th>Description</th></tr></thead><tbody><tr><td><code>"kms"</code></td><td><code>"local"</code></td><td>This indicates that a local Key Management System (KMS) is being used for cryptographic operations, ensuring secure key handling.</td></tr><tr><td><code>"issuerDid"</code></td><td><code>"did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99"</code></td><td>Specifies the issuer's Decentralized Identifier (DID), uniquely identifying the entity responsible for managing and issuing verifiable credentials.</td></tr><tr><td><code>"statusListName"</code></td><td><code>"status-list-encrypted"</code></td><td>Names the Bitstring Status List, providing context for its purpose.</td></tr><tr><td><code>"statusPurpose"</code></td><td><code>["revocation", "suspension", "message"]</code> </td><td>Defines the purposes of the status list as Bitstring status list supports multiple purposes.</td></tr><tr><td><code>"statusSize"</code></td><td><code>2</code></td><td>Defines the size of each bit in the Bitstring Status List.</td></tr><tr><td><code>"statusMessages"</code></td><td><p></p><pre class="language-json5"><code class="lang-json5">[{ "status": "0x0", "message": "valid" },
{ "status": "0x1", "message": "revoked" },
{ "status": "0x2", "message": "suspended" },
{ "status": "0x3", "message": "unknown" }]
</code></pre></td><td>Array of "status" and "message" which defines the message for each bit value. Must be 2^StatusSize </td></tr><tr><td><code>"encrypted"</code></td><td><code>true</code></td><td>Indicates that the Verifiable Credential Status List, crucially, <strong>is encrypted</strong>, enhancing data privacy and security. This can be either <strong>true</strong> or <strong>false (unencrypted).</strong></td></tr><tr><td><code>"paymentConditions"</code></td><td><code>[...]</code></td><td>Describes the payment conditions required for accessing the status list. In this case, a timelock payment mechanism is used, specifying the fee payment address, amount, and interval.</td></tr><tr><td><code>"returnSymmetricKey"</code></td><td><code>true</code></td><td>Specifies that the symmetric key for decrypting the status list will be returned.</td></tr><tr><td><code>"chain"</code></td><td><code>"cheqdTestnet"</code></td><td>Specifies whether the encrypted keys should be broadcast on <code>cheqdMainnet</code> or <code>cheqdTestnet</code></td></tr><tr><td><code>"network"</code></td><td><code>"datil-dev"</code></td><td>Specifies the network where the sharded decryption keys will be stored (across Lit Protocol)</td></tr></tbody></table>

### Step 3: Submit a create Status List transaction to the ledger

Once an Issuer has compiled the Payload file, they can submit this to the ledger using the following transaction:

```bash
veramo execute -m cheqdCreateStatusList --argsFile path/to/payload.json
```

The ledger will acknowledge that this Bitstring status list is created and published. &#x20;

### Encrypted Status List Response format

The following code snippet shows an example of an encrypted Bitstring Status List broadcast on-ledger as a DID-Linked Resource:

```json
{
    "bitstringStatusListCredential": {
        "credentialSubject": {
            "type": "BitstringStatusList",
            "statusPurpose": [
                "revocation",
                "suspension",
                "message"
            ],
            "encodedList": "slojUspvVPYR8aNjCCTdSfGpyBfrakBH-hx2MuKfH4cpqEGs3M5AZacc-tRCDebyflcBavZPsGjiXsY_cl3bK78q0mqNYzx5SpSBHAedD0Vcn4_Z3uA2qsRO9axizuXqcI5RVtnsK_XYjstA3w4V_7Hzsx2mLm9kM_Zmvj_Ob7RHd4ehUPV3_238FmMZDEoZyPwtxwoAVxfMkI01hCz4YszI_TXIG-h1HfMHJygrAkwURsLcoR3lYf-Q0GB2GKGamyLJjS_cBWDpDJG86ENjqb1X8gtqF-9-SpFimFTjJuvdhqtt_EVjIc2vwjddboDHp5T3LxYBQgkC"
        },
        "issuer": {
            "id": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99"
        },
        "type": [
            "VerifiableCredential",
            "BitstringStatusListCredential"
        ],
        "@context": [
            "https://www.w3.org/2018/credentials/v1"
        ],
        "issuanceDate": "2025-07-09T10:38:07.000Z",
        "proof": {
            "type": "JwtProof2020",
            "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSJdLCJ0eXBlIjpbIlZlcmlmaWFibGVDcmVkZW50aWFsIiwiQml0c3RyaW5nU3RhdHVzTGlzdENyZWRlbnRpYWwiXSwiY3JlZGVudGlhbFN1YmplY3QiOnsidHlwZSI6IkJpdHN0cmluZ1N0YXR1c0xpc3QiLCJzdGF0dXNQdXJwb3NlIjpbInJldm9jYXRpb24iLCJzdXNwZW5zaW9uIiwibWVzc2FnZSJdfX0sIm5iZiI6MTc1MjA1NzQ4NywiaXNzIjoiZGlkOmNoZXFkOnRlc3RuZXQ6OWMwMWJjMDUtMTc4Yi00NzQyLWExODktOWI1NjkzM2RmOTcxIn0.98s7gc40-QX49svWGLhBM-GZO1HEaKWdEKsr82V7A7HFxhSNST7DRbI-LjJcBUZBWWYb1Foc5Q7aaWS8LwYTBQ"
        }
    },
    "metadata": {
        "encrypted": true,
        "encoding": "base64url",
        "statusSize": 2,
        "statusMessages": [
            {
                "status": "0x0",
                "message": "valid"
            },
            {
                "status": "0x1",
                "message": "revoked"
            },
            {
                "status": "0x2",
                "message": "suspended"
            },
            {
                "status": "0x3",
                "message": "unknown"
            }
        ],
        "statusListHash": "28d525eae3cfb86ca37d41463a48d069cf42022b6cad7e5370c52b8de464580d",
        "symmetricLength": 97,
        "paymentConditions": [
            {
                "type": "timelockPayment",
                "feePaymentAddress": "cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp",
                "feePaymentAmount": "147603000000000ncheq",
                "intervalInSeconds": 3153600000
            }
        ]
    }
}
```

Importantly, the only encrypted element is the `"encodedList"` element. This provides the Verifier sufficient information to be able to make the payment back the the Issuer and to fulfill the Access Control Conditions, without being able to see the contents of the Status List itself.

By utilizing cryptographic operations, specifying payment requirements, and ensuring data encryption, this configuration enhances the security, integrity, and controlled access to the Status List, aligning with the principles of decentralized and secure identity management.
