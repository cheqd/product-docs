# Charge for Status List

## **Charge for a Credential**

To create an encrypted Verifiable Credential Status List v2021 with a set of Access Control Conditions, an Issuer will need to follow the steps below:

### Step 1: Set up your account

Make sure you have set up your account with Credential Service and are logged in, using our guide below:

* [Set up your account](../set-up-account.md)

### Step 2: Create an Issuer DID

Before you can create an encrypted Status List for charging for Credentials, you need to create a DID which is used to link the Status List on-ledger. Use the API in the page below to create a DID:

* [Create Issuer DID](../dids/create-did.md)

### Step 3: Create an encrypted Status List

When [creating a Status List](../status-lists/), a user will have the following options:&#x20;

<details>

<summary>Encrypted: true</summary>

Setting the value of encrypted to **true** will encrypt the bitstring of the Status List, meaning that a set of Payment Conditions will need to be met in order to access the Status List contents.

</details>

<details>

<summary>Encrypted: false</summary>

Setting the value of encrypted to **false** will create a regular Status List on-ledger, where the contents of the Status List are visible to any users who queries the blockchain.&#x20;

</details>

If a user sets the value to `encrypted:true`, they will then need to input the following information into the payload of the request.

| Parameter             | Value                                            | Description                                                                                                                                                      |
| --------------------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `"feePaymentAddress"` | `"cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp"` | This specifies the cheqd address to which the payment fee should be sent. **This address is associated with the User who wants to charge for Credential Status** |
| `"feePaymentAmount"`  | `"1000"`                                         | This defines the amount of the **payment fee in CHEQ which the Issuer will charge** for a third party to unlock the Credential Status.                           |

For example:

```json
{
  "did": "did:cheqd:testnet:7c2b990c-3d05-4ebf-91af-f4f4d0091d2e",
  "statusListName": "cheqd-employee-credentials"
  "feePaymentAddress": "cheqd1xl5wccz667lk06ahama26pdqvrkz5aws6m0ztp",
  "feePaymentAmount": "1000"
}
```

Once the Issuer has populated the requisite information for the Payment Conditions, they can use the API below to submit the gated Status List to the ledger.&#x20;

{% swagger src="https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json" path="/credential-status/create" method="post" expanded="true" %}
[https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json](https://raw.githubusercontent.com/cheqd/credential-service/main/src/static/swagger.json)
{% endswagger %}

### Encrypted Status List Response format

The following code snippet shows an example of an encrypted Status List response format, which will be published to the ledger:

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

{% hint style="info" %}
Note: The only encrypted element is the "`encodedList`" element. This provides the Verifier sufficient information to be able to make the payment back the the Issuer and to fulfill the [Access Control Conditions](../../sdk/veramo-plugin/payments/access-control-conditions.md), without being able to see the contents of the Status List itself.
{% endhint %}
