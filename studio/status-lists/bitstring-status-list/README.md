# Bitstring Status List

## Get started

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:blue;">Create Bitstring Status List</mark></td><td>Create a Bitstring Status List on cheqd as a DID-Linked Resource</td></tr><tr><td><mark style="color:blue;">Update Bitstring Status List</mark></td><td>Update a Bitstring Status List to revoke/suspend credentials</td></tr><tr><td><mark style="color:blue;">Check Bitstring Status List</mark> </td><td>Check whether a specified indices is revoked, suspended, unsuspended or valid</td></tr><tr><td><mark style="color:blue;">Search Bitstring Status List</mark></td><td>Search for specific entries in the Status List, traversing its history</td></tr></tbody></table>

## Understanding Bitstring Status List

The Bitstring Status List supported in cheqd Studio utilise [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

![Graphic showing the StatusList2021 bitstring](<../../../.gitbook/assets/StatusList21 Bitstring.png>)

_**Figure 1**: Graphic showing the Bitstring Status List bitstring_

Each issued Credential correlates with a position and index on the bitstring, so that a verifier will be able to correlate the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

### Where is the StatusList usually published?

The issuer keeps a bitstring list of all Verifiable Credentials it has issued. The Status List is usually published by the issuer **in the format of its own Verifiable Credential.** This Verifiable Credential is generally hosted publicly on a centralised server or domain to enable third-party read-access.

### Where does cheqd store the Status List?

cheqd stores each Status List and subsequent entries on-ledger as [DID-Linked Resource](../../../architecture/adr-list/adr-002-did-linked-resources.md) versions. This has notable benefits, including the provenance, legitimacy and security of the Status List. For a full list of benefits, see [the context for creating DID-Linked Resources](../../did-linked-resources/understanding-dlrs/context.md).
