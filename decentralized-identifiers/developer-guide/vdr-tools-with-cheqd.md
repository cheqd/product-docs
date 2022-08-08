# Using VDR Tools CLI with cheqd ledger

This page describes how [identity-domain transactions can be integrated in 3rd party applications](README.md) such as [Evernym VDR Tools](https://gitlab.com/evernym/verity/vdr-tools).

## Base write flow

1. **Build a request** _Example_: `build_create_did_request(id, verkey, alias)`
2. **Sign the request using DID key** _Example_: `indy_crypto_sign(did, verkey)`
3. **Build a transaction with the request from previous step** _Example_: `build_tx(pool_alias, pub_key, builded_request, account_number, account_sequence, max_gas, max_coin_amount, denom, timeout_height, memo)`
4. **Sign the transaction** _Example_: `cheqd_keys_sign(wallet_handle, key_alias, tx)`.
5. **Broadcast a signed transaction** _Example_: `broadcast_tx_commit(pool_alias, signed)`.

### Response format

```bash
  Response {
   check_tx: TxResult {
      code: 0,
      data: None,
      log: "",
      info: "",
      gas_wanted: 0,
      gas_used: 0,
      events: [
      ],
      codespace: ""
   },
   deliver_tx: TxResult {
      code: 0,
      data: Some(Data([...])),
      log: "[{\"events\":[{\"type\":\"message\",\"attributes\":[{\"key\":\"action\",\"value\":\"send\"},{\"key\":\"sender\",\"value\":\"cheqd1fknpjldck6n3v2wu86arpz8xjnfc60f99ylcjd\"},{\"key\":\"module\",\"value\":\"bank\"}]},{\"type\":\"transfer\",\"attributes\":[{\"key\":\"recipient\",\"value\":\"cheqds1pvnjjy3vz0ga6hexv32gdxydzxth7f86mekcpg\"},{\"key\":\"sender\",\"value\":\"cheqd1fknpjldck6n3v2wu86arpz8xjnfc60f99ylcjd\"},{\"key\":\"amount\",\"value\":\"1000ncheq\"}]}]}]",
      info: "",
      gas_wanted: 0,
      gas_used: 0,
      events: [...], 
      codespace: ""
   },
   hash: "1B3B00849B4D50E8FCCF50193E35FD6CA5FD4686ED6AD8F847AC8C5E466CFD3E",
   height: 353
}
```

- **`hash`** : Transaction hash

- **`height`**: Ledger height

## DID transactions

### Create DID

#### Command

`build_create_did_request(id, verkey, alias)`

#### Request format

```bash
CreateDidRequest 
{
    "data": {
               "id": "GEzcdDLhCpGCYRHW82kjHd",
               "verkey": "~HmUWn928bnFT6Ephf65YXv",
               "alias": "DID for Alice"
             },
    "owner": "GEzcdDLhCpGCYRHW82kjHd",
    "signature": "49W5WP5jr7x1fZhtpAhHFbuUDqUYZ3AKht88gUjrz8TEJZr5MZUPjskpfBFdboLPZXKjbGjutoVascfKiMD5W7Ba",
    "metadata": {}
}
```

- `id` (base58-encoded string): Target DID as base58-encoded string for 16 or 32 byte DID value
- `verkey` (base58-encoded string, possibly starting with "~~"; optional): Target verification key. It can start with "~~", which means that it is an abbreviated `verkey` and should be 16 bytes long when decoded. Otherwise, it's a full `verkey` which should be 32 bytes long when decoded.
- `alias` (string; optional)

#### Response format

```bash
CreateDidResponse {
    "key": "did:GEzcdDLhCpGCYRHW82kjHd" 
}
```

- `key`(string): A unique key is used to store this DID in a state

#### Response validation

- `CreateDidRequest` must be signed by the DID from `id` field. It means that this DID must be an owner of this DID transaction.

## Update DID

#### Command

`build_update_did_request(id, verkey, alias)`

#### Request format

```bash
UpdateDidRequest 
{
    "data": {
               "id": "GEzcdDLhCpGCYRHW82kjHd",
               "verkey": "~HmUWn928bnFT6Ephf65YXv",
               "alias": "DID for Alice"
             },
    "owner": "GEzcdDLhCpGCYRHW82kjHd",
    "signature": "49W5WP5jr7x1fZhtpAhHFbuUDqUYZ3AKht88gUjrz8TEJZr5MZUPjskpfBFdboLPZXKjbGjutoVascfKiMD5W7Ba",
    "metadata": {}
}
```

- `id` (base58-encoded string): Target DID as base58-encoded string for 16 or 32 byte DID value.
- `verkey` (base58-encoded string, possibly starting with "~~"; optional): Target verification key. It can start with "~~", which means that it is an abbreviated `verkey` and should be 16 bytes long when decoded. Otherwise, it's a full `verkey` which should be 32 bytes long when decoded.
- `alias` (string; optional).

#### Response format

```bash
UpdateDidResponse {
    "key": "did:GEzcdDLhCpGCYRHW82kjHd" 
}
```

- `key`(string): A unique key is used to store this DID in a state

#### Response validation

- A transaction with `id` from `UpdateDidRequest`must already be in a ledger created by `CreateDidRequest`
- `UpdateDidRequest` must be signed by the DID from `id` field. It means that this DID must be an owner of this DID transaction.

## Get DID

#### Command

`build_query_get_did(id)`

- `id` (base58-encoded string): Target DID as base58-encoded string for 16 or 32 byte DID value.

#### Request format

```bash
Request 
{
    "path": "/store/cheqd/key",
    "data": <bytes>,
    "height": 642,
    "prove": true
}
```

- `path`: Path for RPC endpoint for cheqd pool
- `data`: Query with an entity key from a state. String `did:<id>` encoded to bytes
- `height`: Ledger height (size). `None` for auto calculation
- `prove`: Boolean value. `True` for getting state proof in a pool response

#### Response format

```bash
QueryGetDidResponse{
        "did": {
               "id": "GEzcdDLhCpGCYRHW82kjHd",
               "verkey": "~HmUWn928bnFT6Ephf65YXv",
               "alias": "DID for Alice"
             },
}
```
