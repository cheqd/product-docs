---
description: Understand how privacy is protected throughout the credential payment flow.
---

# Privacy Considerations

## Privacy considerations

cheqd's implementation of Credential Payments uses encrypted [DID-Linked Resources](../../did-linked-resources/understanding-dlrs/) (such as a Status Lists), with Payment Conditions required to unlock. Learn about cheqd's Access Control Condition logic below:

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:blue;"><strong>Access Control Conditions</strong></mark></td><td>Understand how cheqd gates encrypted resources using payment conditions and Access Control logic.</td></tr></tbody></table>

There are multiple areas within this flow where privacy needs to be maintained:

1. **Status List construction:** Credential information within Status List should not constitute [Personally Identifiable Information (PII)](https://en.wikipedia.org/wiki/Personal_data).
2. **Verifier pays Issuer:** Any payment for decrypting a Status List should not be correlatable to a Holder presenting or sharing a Credential.

## Status List construction

The [Bitstring Status List specification](https://www.w3.org/TR/vc-bitstring-status-list/), which cheqd encrypts with payment conditions, utilises [bitstrings](https://w3c-ccg.github.io/vc-status-list-2021/#conceptual-framework) to represent whether a Verifiable Credential has been suspended/revoked or not. A bitstring can be thought of as a long list of 1s and 0s, where, if the binary value of the position in the list is 1 (one), the [verifiable credential](https://w3c-ccg.github.io/vc-status-list-2021/#dfn-verifiable-credentials) is revoked, if it is 0 (zero) it is not revoked.

![Graphic showing the Bitstring for a Status List](<../../../.gitbook/assets/StatusList21 Bitstring.png>)

This works by tying each issued Credential with a position and index on the bitstring, so that a Verifier will be able to check the value within the Credential against the public bitstring to ascertain whether the Credential has been revoked or not, using a [validate algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#validate-algorithm) as well as a [bitstring expansion algorithm](https://w3c-ccg.github.io/vc-status-list-2021/#bitstring-expansion-algorithm).

As part of the bitstring construction. there is a minimum bitstring length of 131,072 entries (16kb uncompressed). Moreover, in this string, the indexes that are not specified are randomised between revoked and unrevoked. This means that there will never be a bitstring with a single entry, or a small amount of entries.&#x20;

Using this type of construction, the privacy of the Holder is protected through "Herd Privacy" because there is no way that a third party can externally view the Status List and can correlate indices with a particular individual's credential without direct knowledge of a Holders' credential index.

The [Bitstring Status List specification](https://www.w3.org/TR/vc-bitstring-status-list/) expands on these privacy considerations.

## Verifier pays Issuer

As part of the Access Control Condition setup, the Issuer can set a **single payment fee** for meeting the Access Control Conditions and decrypting a particular Status List.&#x20;

Through setting a flat price for decryption, there is no price variation from checking one persons' credential status versus another's. This means that for a particular Status List, all payments on the network will be made at the same price.&#x20;

Once again, this creates a layer of "Herd Privacy" for the Holder, since **the Credential index itself is decoupled from the payment fee**. Therefore. while transactions will be visible on the network, there will be no way for a third party to surveil the network or draw conclusions about who's Credential status is being checked.&#x20;

If there was a variation in the price per Credential status check within the same status list, this would be more likely to become correlatable, alongside other information, back to a specific individual.&#x20;

## Continual improvement

We will continue to improve and assess the privacy considerations as we roll out Credential Payments. Some areas we believe there may be scope for privacy leakage are as follows:

* **Holders' Credential index is doxxed**: If a Holder has a particular Credential index doxxed, as well as a link to the status list, there may be scope to monitor the Credential Status of that individual, through a payment. However, third parties will not know when that Holder is using their Credentials.&#x20;
* **Issuer creates single entry Status List with specific unlock price:** If an Issuer does not use our preset Status List length and creates their own resource with a single or few entries, they may provide a higher likelihood of doxxing when an individual uses their Credential. This is why cheqd, and the specification, has enforced a minimum Status List length.
