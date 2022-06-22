### Overview

[cheqd Cosmos CLI](https://github.com/cheqd/cheqd-node/blob/4b36c0a5f767b7d4fb91341bc732d54471702dac/docs/cheqd-cli/README.md) can be used for managing Decentralised Identifiers (DIDs) and DID Documents, as well as updating existing DIDs based on the [`did:cheqd` DID method](https://github.com/cheqd/cheqd-node/blob/4b36c0a5f767b7d4fb91341bc732d54471702dac/architecture/adr-list/adr-002-cheqd-did-method.md).

**Crypto commands in cheqd CLI - EXPERIMENTAL FUNCTIONALITY - Do not use in production!**

For creating such DID-Docs the [next document](./create-did-and-did-document.md) can be used.
Current document is mostly about updating already existed DID-Doc.

#### Update DID Document

Assumptions:

- Cause it's not a production solution, we assume that the user stored somewhere public/private keys during the DID-Doc creation process.
- For updating some of part in DID-Doc it's required to send the whole updated DID-Doc. It's not possible for now to send just changed fields/parts.

Arguments:

* `DIDDoc_in_JSON`: A string with updated DID Document in Json format. `id` is not changeable field and mast be used from creation transaction. Base example:

```
{
  "id": "did:cheqd:<namespace>:<unique-id>",
  "verification_method": [
    {
      "id": "did:cheqd:<namespace>:<unique-id>#<key-alias>",
      "type": "Ed25519VerificationKey2020",
      "controller": "did:cheqd:<namespace>:<unique-id>",
      "public_key_multibase": "<verification-public-key-multibase>"
    }
  ],
  "authentication": [
    "did:cheqd:<namespace>:<unique-id>#<auth-key-alias>"
  ]
}
```

* `id` feild for `verification_method` section: key identifier in the following format: `did:cheqd:<namespace>:<unique-id>#<key-alias>`. Exapmle: `did:cheqd:testnet:zJ5EDiiiKWDyo79n#key1`.
*  Base64 encoded ed25519 private key to sign identity message with. A pair for the key which was used for the DID creation.\
  Use for testing purposes only because the key will be stored in shell history!
* `--from`: Cosmos account key which will pay fees for the transaction ordering.
* `--node`: IP address or URL of node to send request to
* `--chain-id`: i.e. `cheqd-testnet-4`
* `--fees`: Maximum fee limit that is allowed for the transaction.

Command:

```
cheqd-noded tx cheqd update-did <DIDDoc_in_JSON> <did_verification_method_id> <identity_private_key_BASE_64> \
  --from <cosmos_account> --node <url> --chain-id <chain> --fees <fee>
```

Example:

```
cheqd-noded tx cheqd update-did '{"id": "did:cheqd:testnet:zJ5EDiiiKWDyo79n",\
                                   "verification_method": [{\
                                     "id": "did:cheqd:testnet:zJ5EDiiiKWDyo79n#key1",\
                                     "type": "Ed25519VerificationKey2020",\
                                     "controller": "id:cheqd:testnet:zJ5EDiiiKWDyo79n",\
                                     "public_key_multibase": "zCeJfYbiFoUcENEjuxnU9ez6VBZjxavTjSZtHP6y226fp"\
                                   }],\
                                   "authentication": [\
                                     "id:cheqd:testnet:zJ5EDiiiKWDyo79n#key1"\
                                   ]\
                                 }' \
  "id:cheqd:testnet:zJ5EDiiiKWDyo79n#key1" \
  "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q==" \
  --from my_account --node http://nodes.testnet.cheqd.network:26657 --chain-id cheqd-testnet-4 --fees 5000000ncheq
```

BTW, it can be more useful to prepare json file using your favorite editor and after that run the command as:

```
cheqd-noded tx cheqd update-did "$(cat diddoc.json)" "FxaJOy4HFoC2Enu1SizKtU0L+hmBRBAEpC+B4TopfQoyetOF5T68Ks3db5Yy9ykFdgEboPUes3m6wvXNLpbv+Q==" \
  --from my_account --node http://nodes.testnet.cheqd.network:26657 --chain-id cheqd-testnet-4 --fees 5000000ncheq
```
