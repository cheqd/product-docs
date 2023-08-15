# Verifier pays issuer

## Verifier pays issuer

If a Verifier wants to unlock access to the Resource, to gain access to additional information about a Credential presented to them, such as the Credential Status, firstly, the Verifier will be presented Credential, including a link to the StatusList. The Verifier will be directed to a resource with the following information through a DID URL, for example:&#x20;

{% embed url="https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:322761ea-587d-454a-a955-745200301b99?resourceName=revocation-list-encrypted-inverse-timelock&resourceType=StatusList2021Revocation" %}
Example of encrypted Status List identifiable via a DID URL
{% endembed %}

This DID URL will contain:

* An encrypted potion of the Resource, such as a Status List bitstring
* Unencrypted metadata about the Resource, including the issuers' payment address and and the Access Control Conditions.

This gives the verifier requisite information they need in order to pay the Issuer to unlock the Credential Status.&#x20;

If they make a payment of the specified amount in the payment conditions back to the Issuer, the [Access Control Conditions](understanding-access-control-conditions.md) will be met, and verifier will obtain the keys to decrypt the Status List and access the content.&#x20;
