# Understanding Access Control Conditions

## Access Control Conditions

The Access Control Conditions outlined in this section establish a secure and structured framework for unlocking encrypted Status Lists and other DID-Linked Resource.&#x20;

By employing a combination of Decentralised Identifiers (DIDs), DID-Linked Resources, encryption, and timed payment mechanisms, these access control conditions safeguard the integrity of the Resource, promote transparency, and control access to its data.

### Request format: Payment conditions

For Credential Payments, the predominant Access Control Condition is a **payment made from the Verifier of a Credential back to the Issuer**.

The payment conditions outlined in the code snippet below define the payment requirements that **must** be met to access and interact with an encrypted Resource. These are written into a `payload.json` file when a create Status List transaction is made to the ledger ([see section below on creating an encrypted Status List](understanding-access-control-conditions.md#creating-an-encrypted-status-list)).

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

If a Payment Condition is set upon creation of a Status List or other Resource, this will populate an Access Control Condition which is broadcasted across series of nodes to monitor whether the conditions have been met. The Access Control Condition uses query language to directly fetch information from the cheqd ledger, and is fully derived from the inputs made into the Payment Condition.

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

Once an Access Control Condition has been set, the decryption keys are sharded between an array of nodes to prevent any single node or few malicious nodes from decrypting the data. This comprehensive setup ensures secure and controlled access to the specified resource based on specified criteria and events from the cheqd blockchain.&#x20;
