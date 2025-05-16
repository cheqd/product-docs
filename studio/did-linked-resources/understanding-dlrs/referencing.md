---
description: >-
  Understand how to reference a DID-Linked Resource (DLR) within the body of a
  W3C Verifiable Credential.
---

# Referencing DID-Linked Resources in VCs

Verifiable Credentials (VCs) often need to reference external data to provide structure, governance, or context. On the **cheqd network**, these external references can be anchored as **DID-Linked Resources**, enabling permanent, tamper-evident, and verifiable links within the credential itself.

This tutorial explains how to reference DID-Linked Resources in the body of a VC using fully qualified `did:cheqd` URLs. These references enhance trust, interoperability, and auditability of issued credentials.

### Common Resources to Reference in a VC

The table details what types of DID-Linked Resources may commonly be included within the body of a Verifiable Credential:

| Resource Type                | Use Case                                                                  |
| ---------------------------- | ------------------------------------------------------------------------- |
| **Credential Schemas**       | Define the expected structure and semantics of a credential               |
| **Trust Registries**         | Prove that the issuer is authorised to issue a particular credential      |
| **Status Lists**             | Reference revocation registries (for AnonCreds or Bitstring Status Lists) |
| **Logos & Visuals**          | Display issuer branding or credential type icons in wallets               |
| **Legal Terms & Governance** | Point to frameworks, policies, or usage restrictions                      |

### Example: Event Ticket Credential with Schema & Logo References

Below is a Verifiable Credential issued for an event ticket. It references:

* A **schema**, published as a DID-Linked Resource
* A **logo**, hosted as a visual asset under the same issuer DID

```json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://veramo.io/contexts/profile/v1",
    "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:z6jKUJA5YcZsNxZgsrQPKPipL2FRTf4s/resources/a20aa56a-a76f-4828-8a98-4c85d9494545"
  ],
  "type": [
    "VerifiableCredential",
    "EventReservation"
  ],
  "issuanceDate": "2022-11-17T03:19:03.328Z",
  "credentialSubject": {
    "id": "did:key:z6MkwTr46BWU42YejmZrDDCbL127dBoxSXeLuaHS75vTCg7i"
  },
  "reservationId": "4810116769",
  "reservationStatus": "https://schema.org/ReservationConfirmed",
  "reservationFor": {
    "@type": "Event",
    "name": "Internet Identity Workshop IIWXXXV",
    "startDate": "2022-11-16T16:00:00",
    "endDate": "2022-11-18T00:00:00",
    "location": "Computer History Museum, 1401 N Shoreline Blvd, Mountain View, CA 94043",
    "logo": "https://resolver.cheqd.net/1.0/identifiers/did:cheqd:testnet:z6jKUJA5YcZsNxZgsrQPKPipL2FRTf4s/resources/8140ec3a-d8bb-4f59-9784-a1cbf91a4a35"
  },
  "issuer": {
    "id": "did:cheqd:mainnet:zAXwwqZzhCZA1L77ZBa8fhVNjL9MQCHX"
  },
  "proof": {
    "type": "JwtProof2020",
    "jwt": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJ2YyI6eyJAY29udGV4dCI6WyJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy92MSIsImh0dHBzOi8vdmVyYW1vLmlvL2NvbnRleHRzL3Byb2ZpbGUvdjEiLCJodHRwczovL3Jlc29sdmVyLmNoZXFkLm5ldC9kaWQ6Y2hlcWQ6dGVzdG5ldDp6NmpLVUpBNVljWnNOeFpnc3JRUEtQaXBMMkZSVGY0cy9yZXNvdXJjZXMvYTIwYWE1NmEtYTc2Zi00ODI4LThhOTgtNGM4NWQ5NDk0NTQ1Il0sInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJFdmVudFJlc2VydmF0aW9uIl0sImNyZWRlbnRpYWxTdWJqZWN0Ijp7ImlkIjoiZGlkOmtleTp6Nk1rd1RyNDZCV1U0Mlllam1ackREQ2JMMTI3ZEJveFNYZUx1YUhTNzV2VENnN2kifX0sIkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIiwiaHR0cHM6Ly92ZXJhbW8uaW8vY29udGV4dHMvcHJvZmlsZS92MSIsImh0dHBzOi8vcmVzb2x2ZXIuY2hlcWQubmV0L2RpZDpjaGVxZDp0ZXN0bmV0Ono2aktVSkE1WWNac054WmdzclFQS1BpcEwyRlJUZjRzL3Jlc291cmNlcy9hMjBhYTU2YS1hNzZmLTQ4MjgtOGE5OC00Yzg1ZDk0OTQ1NDUiXSwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIkV2ZW50UmVzZXJ2YXRpb24iXSwiaXNzdWFuY2VEYXRlIjoiMjAyMi0xMS0xN1QwMzoxOTowMy4zMjhaIiwiY3JlZGVudGlhbFN1YmplY3QiOnsiaWQiOiJkaWQ6a2V5Ono2TWt3VHI0NkJXVTQyWWVqbVpyRERDYkwxMjdkQm94U1hlTHVhSFM3NXZUQ2c3aSJ9LCJyZXNlcnZhdGlvbklkIjoiNDgxMDExNjc2OSIsInJlc2VydmF0aW9uU3RhdHVzIjoiaHR0cHM6Ly9zY2hlbWEub3JnL1Jlc2VydmF0aW9uQ29uZmlybWVkIiwicmVzZXJ2YXRpb25Gb3IiOnsiQHR5cGUiOiJFdmVudCIsIm5hbWUiOiJJbnRlcm5ldCBJZGVudGl0eSBXb3Jrc2hvcCBJSVdYWFhWIiwic3RhcnREYXRlIjoiMjAyMi0xMS0xNlQxNjowMDowMCIsImVuZERhdGUiOiIyMDIyLTExLTE4VDAwOjAwOjAwIiwibG9jYXRpb24iOiJDb21wdXRlciBIaXN0b3J5IE11c2V1bSwgMTQwMSBOIFNob3JlbGluZSBCbHZkLCBNb3VudGFpbiBWaWV3LCBDQSA5NDA0MyIsImxvZ28iOiJodHRwczovL3Jlc29sdmVyLmNoZXFkLm5ldC8xLjAvaWRlbnRpZmllcnMvZGlkOmNoZXFkOnRlc3RuZXQ6ejZqS1VKQTVZY1pzTnhaZ3NyUVBLUGlwTDJGUlRmNHMvcmVzb3VyY2VzLzgxNDBlYzNhLWQ4YmItNGY1OS05Nzg0LWExY2JmOTFhNGEzNSJ9LCJpc3N1ZXIiOnsiaWQiOiJkaWQ6Y2hlcWQ6bWFpbm5ldDp6QVh3d3FaemhDWkExTDc3WkJhOGZoVk5qTDlNUUNIWCJ9LCJzdWIiOiJkaWQ6a2V5Ono2TWt3VHI0NkJXVTQyWWVqbVpyRERDYkwxMjdkQm94U1hlTHVhSFM3NXZUQ2c3aSIsIm5iZiI6MTY2ODY1NTE0MywiaXNzIjoiZGlkOmNoZXFkOm1haW5uZXQ6ekFYd3dxWnpoQ1pBMUw3N1pCYThmaFZOakw5TVFDSFgifQ.U4vPbvdY7724a1jJwiDeCH_4_YC5sKUMcH6lY_XCVWBTE1RvYAnTj4fPHWMy6zSVFL9TAk4ZBOqFjKUtShBSCw"
  }
}
```

### Dereferencing the schema

The VC references a schema hosted as a DID-Linked Resource. Resolving this DID URL returns:

```json
{
  "@context": {
    "id": "@id",
    "type": "@type",
    "@version": 1.1,
    "schema": "http://schema.org/#",
    "xsd": "https://www.w3.org/2001/XMLSchema#",
    "reservationId": {
      "@id": "schema: reservationId",
      "@type": "xsd:string"
    },
    "reservationStatus": "schema: reservationStatus",
    "reservationFor": {
      "@id": "schema: reservationFor",
      "@type": "@id",
      "@context": {
        "name": "schema: name",
        "startDate": "schema: startDate",
        "endDate": "schema: endDate",
        "location": {
          "@id": "schema: location",
          "@type": "xsd: string"
        },
        "logo": "schema: logo"
      }
    }
  }
}
```
