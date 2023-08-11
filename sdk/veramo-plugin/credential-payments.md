---
description: Getting started with Payment Rails for Verifiable Credentials
---

# Credential Payments

## Understanding Credential Payments

Credential Payments brings a significant evolution to the Verifiable Credentials landscape, allowing **verifiers to directly pay issuers to unlock Credential Status information**. Underneath the hood, we've engineered a robust and secure payment flow that streamlines the end-to-end payment process, with accuracy, speed, and cryptographic integrity.

To integrate "Credential Payments" into your existing identity infrastructure, you need to understand these three concepts:

1. [**Access Control Conditions**](credential-payments.md#access-control-conditions)
2. [**Creating an encrypted Resource**](credential-payments.md#creating-an-encrypted-resource)
3. [**Verifiable Credentials Status List v2021**](credential-payments.md#verifiable-credentials-and-status-list-v2021)
4. [**Encrypted vs Unencrypted**](credential-payments.md#encrypted-vs-unencrypted)
5. [**How a Verifier pays an Issuer**](credential-payments.md#how-a-verifier-pays-an-issuer)

## Access Control Conditions

The Access Control Conditions outlined in this section establish a secure and structured framework for unlocking encrypted Status Lists and other DID-Linked Resource.&#x20;

By employing a combination of Decentralised Identifiers (DIDs), DID-Linked Resources, encryption, and timed payment mechanisms, these access control conditions safeguard the integrity of the Resource, promote transparency, and control access to its data. This comprehensive approach fosters a secure and privacy-preserving environment for accessing on-chain data, and ultimately for creating a robust payment model between a Credential Verifier and Issuer.&#x20;

### Request format: Payment conditions

For Credential Payments, the predominant Access Control Condition is a **payment made from the Verifier of a Credential back to the Issuer**.

The payment conditions outlined in the code snippet below define the payment requirements that **must** be met to access and interact with an encrypted Resource. These are written into a `payload.json` file when a create Status List transaction is made to the ledger ([see section below on creating an encrypted Status List](credential-payments.md#creating-an-encrypted-status-list)).

These conditions are established to ensure that authorized entities or users contribute a specified fee before gaining access to the Resource.&#x20;

```json
    "paymentConditions": [
        {
            "type": "timelockPayment",
            "feePaymentAddress": "cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp",
            "feePaymentAmount": "147603000000000ncheq",
            "intervalInSeconds": 3153600000
        }
    ],
```

The parameters within the payment conditions are defined as follows:

| Parameter             | Value                                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| --------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"type"`              | `"timelockPayment"`                              | Indicates that a timelock payment mechanism is employed. Currently, this is the only type that is supported.                                                                                                                                                                                                                                                                                                                                                           |
| `"feePaymentAddress"` | `"cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp"` | Specifies the cheqd address to which the payment fee should be sent. This address is associated with the entity managing the Status List (Issuer) and who will receive the payment.                                                                                                                                                                                                                                                                                    |
| `"feePaymentAmount"`  | `"147603000000000ncheq"`                         | Defines the amount of the payment fee. In this case, the fee is specified in the smallest unit of CHEQ, (ncheq).                                                                                                                                                                                                                                                                                                                                                       |
| `"intervalInSeconds"` | `3153600000`                                     | Sets the duration of the timelock interval, measured in seconds. The verifier must make the payment within the specified time frame relative to a particular block-time on the cheqd blockchain. If the payment is successfully confirmed within this interval, the Verifier's access to the Resource is granted. However, if the payment claim is made outside of this window, it may be considered invalid, and the verifier's access to the Resource may be denied. |

By including these payment conditions in the Status List Payload file, the ledger enforces a financial gate from verifiers seeking access. Additionally, the timelock mechanism adds a layer of time-based verification to the payment process. With the timelock, the payment can only be made in a specific time-interval from the latest block-time on the cheqd network. This ensures that historical payments cannot be used to meet the access control conditions, and fresh payments need to be made to access the Resource, helping to maintain the integrity of the payment and access process.

### Response format: Access Control Conditions

If a Payment Condition is set upon creation of a Status List or other Resource, this will populate an Access Control Condition. The Access Control Condition uses query language to directly fetch information from the cheqd ledger, and is fully derived from the inputs made into the Payment Condition.

```json
{
    "unifiedAccessControlCondition": {
        "conditionType": "cosmos",
        "path": "/cosmos/tx/v1beta1/txs?events=transfer.recipient='cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp'&events=transfer.amount='147603000000000ncheq'&order_by=2&pagination.limit=1",
        "chain": "cheqdMainnet",
        "method": "timelock",
        "parameters": ["latest"],
        "returnValueTest": {
            "key": "$.tx_responses.*.timestamp",
            "comparator": "<=",
            "value": "3153600000"
        }
    },
    "returnTxResponse": true
}

```

Each of these conditions are defined as follows:

| Parameter         | Value                                                                                                                                                                           | Description                                                                                                                                                                                                                                   |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"conditionType"` | `"cosmos"`                                                                                                                                                                      | Specifies the type of condition being employed, indicating that the access control condition is related to the Cosmos blockchain ecosystem                                                                                                    |
| `"path"`          | `"/cosmos/tx/v1beta1/txs?events=transfer.recipient='cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp'&events=transfer.amount='147603000000000ncheq'&order_by=2&pagination.limit=1"` | Defines the query path to retrieve specific transaction events from the cheqd blockchain. It filters transactions with a recipient address and amount matching certain criteria, orders the results, and limits the query to one transaction. |
| `"chain"`         | `"cheqdMainnet"`                                                                                                                                                                | Specifies the blockchain network (chain) being used, indicating that the condition applies to the cheqd Mainnet.                                                                                                                              |
| `"method"`        | `"timelock"`                                                                                                                                                                    | Specifies the access control method being utilized, which is a timelock mechanism.                                                                                                                                                            |
| `"parameters"`    | `"latest"`                                                                                                                                                                      | Provides parameters for the access control method. In this case, it refers to the "latest" state on the blockchain                                                                                                                            |
| `"key"`           | `"$.tx_responses.*.timestamp"`                                                                                                                                                  | Specifies the key path within the transaction responses to extract the timestamp of the transaction.                                                                                                                                          |
| `"comparator"`    | `"<="`                                                                                                                                                                          | This checks if the extracted timestamp is less than or equal to the provided value.                                                                                                                                                           |
| `"value"`         | `"3153600000"`                                                                                                                                                                  | Defines the value (in seconds) against which the extracted timestamp is compared. This value represents a time interval.                                                                                                                      |

This comprehensive setup ensures secure and controlled access to the specified resource based on specified criteria and events from the cheqd blockchain.&#x20;

## **Charge for a Credential**

To create an encrypted Verifiable Credential Status List v2021 with a set of Access Control Conditions, an Issuer will need to:

1. Compile a `payload.json` file with requisite Access Control Conditions;
2. Submit a `create Status List` transaction the the ledger.

### Step 1: Create a DID

* [Create a DID](did-operations/create-did.md)

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



## **Verifiable Credentials and Status List v2021**

Currently, cheqd supports creating unencrypted and encrypted Status List 2021 Resources to enable issuers to gate the status of Credentials they issue.&#x20;

The tutorials below explain:

1. [What is Verifiable Credential Status List v2021?](status-list-v2021/)
2. [How to Create a Status List as a Resource](status-list-v2021/statuslist2021-resources.md)
3. [Issuing a Verifiable Credential referencing a Status List](status-list-v2021/issuing-a-verifiable-credential-referencing-status-list.md)

## Verifier pays issuer

If a Verifier wants to unlock access to the Resource, to gain access to additional information about a Credential presented to them, such as the Credential Status, firstly, the Verifier will be presented Credential, including a link to the StatusList. The Verifier will be directed to a resource with the following information through a DID URL, for example:&#x20;

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=revocation-list-encrypted-inverse-timelock&resourceType=StatusList2021Revocation" %}
Example of encrypted Status List identifiable via a DID URL
{% endembed %}

This DID URL will contain:

* An encrypted potion of the Resource, such as a Status List bitstring
* Unencrypted metadata about the Resource, including the issuers' payment address and and the Access Control Conditions.
