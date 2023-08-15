# Charge for Status List

## **Charge for a Credential Status List**

To create an encrypted Verifiable Credential Status List v2021 with a set of Access Control Conditions, an Issuer will need to:

1. Compile a `payload.json` file with requisite Access Control Conditions;
2. Submit a `create Status List` transaction the the ledger.

### Step 1: Create a DID

* [Create a DID](../did-operations/create-did.md)

### Step 2: Compiling a Status List 2021 Payload file

Below are examples of encrypted Status List Payload files:&#x20;

<details>

<summary>Revocation: StatusList2021</summary>

```json
{
    "kms": "local",
    "issuerDid": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99",
    "statusListName": "revocation-list-encrypted-inverse-timelock",
    "statusPurpose": "revocation",
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
        "chain": "cheqdMainnet",
        "network": "localhost"
    }
}

```

</details>

<details>

<summary>Suspension: StatusList2021</summary>

```json
{
    "kms": "local",
    "issuerDid": "did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99",
    "statusListName": "suspension-list-encrypted-inverse-timelock",
    "statusPurpose": "suspension",
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
        "chain": "cheqdMainnet",
        "network": "localhost"
    }
}

```

</details>

The provided code snippets above show two Status List Payload files for both Revocation and Suspension.&#x20;

The Payload files are a crucial configuration element that establishes Payment Conditions for unlocking a Status List Resource. Let's break down the components of this code to understand how it achieves this access control mechanism:



| Parameter              | Value                                                      | Description                                                                                                                                                                            |
| ---------------------- | ---------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"kms"`                | `"local"`                                                  | This indicates that a local Key Management System (KMS) is being used for cryptographic operations, ensuring secure key handling.                                                      |
| `"issuerDid"`          | `"did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99"` | Specifies the issuer's Decentralized Identifier (DID), uniquely identifying the entity responsible for managing and issuing verifiable credentials.                                    |
| `"statusListName"`     | `"revocation-list-encrypted"`                              | Names the Verifiable Credential Status List, providing context for its purpose.                                                                                                        |
| `"statusPurpose"`      | `"revocation"`                                             | Defines the purpose of the status list. This can be either **revocation** or **suspension.**                                                                                           |
| `"encrypted"`          | `true`                                                     | Indicates that the Verifiable Credential Status List, crucially, **is encrypted**, enhancing data privacy and security. This can be either **true** or **false (unencrypted).**        |
| `"paymentConditions"`  | `[...]`                                                    | Describes the payment conditions required for accessing the status list. In this case, a timelock payment mechanism is used, specifying the fee payment address, amount, and interval. |
| `"returnSymmetricKey"` | `true`                                                     | Specifies that the symmetric key for decrypting the status list will be returned.                                                                                                      |
| `"dkgOptions"`         | `{...}`                                                    | Configures Distributed Key Generation (DKG) options, including the blockchain and network information.                                                                                 |



### Step 3: Submit a create Status List transaction to the ledger

Once an Issuer has compiled the Payload file, they can submit this to the ledger using the following transaciton:

```
veramo execute -m cheqdCreateStatusList2021 --argsFile path/to/payload.json
```

The ledger will acknowledge that this StatusList bitstring should be This indicates to the ledger that the Resource should be fully encrypted and only be decrypted upon the set conditions being met.&#x20;

### Encrypted Status List Response format

The following code snippet shows an example of an encrypted Status List:

```json
{
  "StatusList2021": {
    "statusPurpose": "revocation",
    "encodedList": "803fda8136009b4c92d40f8740a19ec4ca9eebe6acd97015fba90b393ea117e746ac9b60a7d44297796f1fc5214b71a4bd17be9c09f32fd0c5d53c32f1c6bfe796b2e8e011c77bf9538c5c57c567676d99a9327284ba377ae90c17e42856971f",
    "validFrom": "2023-07-06T08:27:44.121Z"
  },
  "metadata": {
    "type": "StatusList2021Revocation",
    "encrypted": true,
    "encoding": "base64url",
    "encryptedSymmetricKey": "1e22748c86db6fa906e1e94aeedc7ca7c706d95f518b239b865411d36c53db2da7afd67ad11a69a99b16cee73e807c174da700b421cd5abdc06e1871ad87137bfe6fc52d64c90239e3366aecf69d60581e38d7fc9da0e32461e6640f3099628437a10f219cb89ff0d1aa6cd803288d335d759d8e16abffeb485ed277e17274cb0000000000000020ad03178dd1550e9622365abf929d96600ab004fb96152bf595bef665424943c56461b30a993221efc4812a30dd2f56a3",
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

Importantly, the only encrypted element is the "encodedList" element. This provides the Verifier sufficient information to be able to make the payment back the the Issuer and to fulfill the Access Control Conditions, without being able to see the contents of the Status List itself.

By utilizing cryptographic operations, specifying payment requirements, and ensuring data encryption, this configuration enhances the security, integrity, and controlled access to the Status List, aligning with the principles of decentralized and secure identity management.
